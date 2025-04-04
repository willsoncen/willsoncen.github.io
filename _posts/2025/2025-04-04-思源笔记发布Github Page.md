---
layout: post
category: 计算机
title: 思源笔记发布Github Page
tags: [计算机,ubuntu]
---

# 思源笔记发布Github Page

​`前提：`​你已经拥有了一个GitHub page的个人网站，并且你的操作系统是ubuntu

# 一、修改思源笔记的导出选项，自动添加元元素

# 二、编写ubuntu的bash文件进行md文件的转移

* 使用 inotifywait（Linux 文件系统事件监控工具）代替轮询。inotifywait 可以实时监听目录变化，只有在文件创建或修改时触发操作。

  * 安装：···

    ```
    sudo apt install inotify-tools
    ```

* 编写siyuan_to_github.sh

```
#!/bin/bash

# 配置
source_dir="/home/willson/下载"
target_dir="/home/video/repo/willsoncen.github/_posts/2025"
default_layout="post"
default_category="uncategorized"
log_file="/home/willson/md_processor.log"

mkdir -p "$target_dir"

interactive=1
if [[ "$1" == "--no-interact" ]]; then
  interactive=0
fi

log() {
  echo "$(date): $1" >> "$log_file"
}

if ! command -v inotifywait &> /dev/null; then
  log "inotifywait not found, please install inotify-tools"
  exit 1
fi

if ! command -v zenity &> /dev/null && [[ $interactive -eq 1 ]]; then
  log "zenity not found, falling back to non-interactive mode"
  interactive=0
fi

log "Starting Markdown processor service"
inotifywait -m "$source_dir" -e create -e moved_to |
  while read -r directory events filename; do
    if [[ "$filename" =~ \.zip$ ]]; then
      new_zip="$source_dir/$filename"
      if unzip "$new_zip" -d "$source_dir" &> /dev/null; then
        md_file=$(find "$source_dir" -maxdepth 1 -type f -name "*.md" -not -name "$(basename "$new_zip" | sed 's/\.zip$//').md")

        if [[ -n "$md_file" ]]; then
          timestamp=$(date +"%Y-%m-%d")
          filename_without_ext=$(basename "$md_file" .md)
          new_filename="${timestamp}-${filename_without_ext}.md"
          new_path="${target_dir}/${new_filename}"

          file_content=$(cat "$md_file")
          metadata_block=$(echo "$file_content" | awk '
            /^---$/ && !seen_first {seen_first=1; next}
            seen_first && /^---$/ {seen_second=1; exit}
            seen_first && !seen_second {print}
          ')
          content=$(echo "$file_content" | awk '
            BEGIN {print_content=0}
            /^---$/ && !seen_first {seen_first=1; next}
            seen_first && /^---$/ && !seen_second {seen_second=1; print_content=1; next}
            print_content {print}
          ')

          if [[ -z "$metadata_block" ]]; then
            log "Warning: No valid metadata block found in $md_file, using defaults"
            metadata_block="title: $filename_without_ext"
          fi

          IFS=$'
' read -r -d '' -a metadata_lines <<< "$metadata_block"

          if [[ $interactive -eq 1 ]]; then
            category=$(zenity --entry \
              --title="Markdown Processor" \
              --text="Enter category for $new_filename (default: $default_category)" \
              --entry-text="$default_category" 2>> "$log_file")
            if [[ $? -ne 0 ]]; then
              log "zenity failed to run, falling back to default category"
              category="$default_category"
            elif [[ -z "$category" ]]; then
              category="$default_category"
            fi
          else
            category="$default_category"
            log "Non-interactive mode: Using default category '$category' for $new_filename"
          fi

          new_metadata="---
layout: $default_layout
category: $category"
          for line in "${metadata_lines[@]}"; do
            if [[ "$line" =~ ^title: ]]; then
              new_metadata="$new_metadata
$line"
            elif [[ "$line" =~ ^tags: ]]; then
              new_metadata="$new_metadata
$line"
            fi
          done
          new_metadata="$new_metadata
---"

          if echo -e "$new_metadata
$content" > "$new_path"; then
            log "Processed: $new_filename with category '$category'"
            rm "$md_file"
          else
            log "Error: Failed to write $new_path"
          fi
        fi
        rm "$new_zip"
      else
        log "Error: Failed to unzip $new_zip"
      fi
    fi
  done
```

‍

# 三、开机自启（使用 systemd）

1. **创建服务文件**：将以下内容保存为 /etc/systemd/system/md-processor.service：

    ```
    [Unit]
    Description=Markdown File Processor for GitHub Pages
    After=network.target

    [Service]
    ExecStart=/path/to/your/script.sh
    Restart=always
    User=willson
    Environment=DISPLAY=:0  # 确保 GUI 弹窗可用
    Environment=XAUTHORITY=/home/willson/.Xauthority  # X11 认证文件路径

    [Install]
    WantedBy=multi-user.target
    ```

    * 将 /path/to/your/script.sh 替换为脚本的实际路径（例如 /home/willson/md\_processor.sh）。
    * User\=willson 确保以你的用户身份运行（调整为实际用户名）。
    * Environment 设置是为了支持 GUI 弹窗（需要 X11 环境）。
2. **启用服务**：

    ```
    sudo systemctl daemon-reload
    sudo systemctl enable md-processor.service
    sudo systemctl start md-processor.service
    ```
3. **检查状态**：

    ```
    sudo systemctl status md-processor.service
    ```

# 四、GUI 交互（使用 zenity）

* **安装 zenity**：

  ```
  sudo apt install zenity  # Debian/Ubuntu
  #sudo yum install zenity  # CentOS/RHEL
  ```
