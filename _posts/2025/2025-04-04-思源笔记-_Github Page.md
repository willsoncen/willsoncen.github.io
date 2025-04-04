---
title: 思源笔记->Github Page
date: 2025-04-04T17:12:39Z
lastmod: 2025-04-04T18:03:40Z
tags: [计算机,ubuntu]
---

# 思源笔记->Github Page

　　​`前提：`​你已经拥有了一个GitHub page的个人网站，并且你的操作系统是ubuntu

# 一、修改思源笔记的导出选项，自动添加元元素
​　　‍

# 二、编写ubuntu的bash文件进行md文件的转移

```
#!/bin/bash

# 配置
source_dir="/home/willson/下载"
target_dir="/home/video/repo/willsoncen.github/_posts/2025"
#repo_path="/home/video/repo/willsoncen.github/"

# 监控目录
while true; do
  # 查找新的 ZIP 文件

  new_zip=$(find "$source_dir" -maxdepth 1 -type f -name "*.zip" -cmi>

  if [[ -n "$new_zip" ]]; then
    # 解压 ZIP 文件
    unzip "$new_zip" -d "$source_dir"

    # 查找解压后的 Markdown 文件
    md_file=$(find "$source_dir" -maxdepth 1 -type f -name "*.md" -no>

    if [[ -n "$md_file" ]]; then
      # 生成符合 GitHub Pages 命名规范的文件名
      timestamp=$(date +"%Y-%m-%d-")
      new_filename="${timestamp}$(basename "$md_file")"
      new_path="${target_dir}/${new_filename}"

      # 移动 Markdown 文件
      mv "$md_file" "$new_path"

      # Git 操作
     # cd "$repo_path"
     # git add "$new_path"
     # git commit -m "Add new post: ${new_filename}"
     #  git push origin main

      echo "Processed: ${new_filename}"
    fi

   # 删除zip文件。
    rm "$new_zip"

  fi

  # 每隔一段时间检查一次
  sleep 60
done
```
