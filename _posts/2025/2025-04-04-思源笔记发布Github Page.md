---
layout: post
title: 思源笔记发布Github Page
${sy_title}
tags: [计算机, ubuntu
${sy_tags}]
date: 2025-04-04
${sy_date:-$(date +"%Y-%m-%d")}
---

---
title: 思源笔记发布Github Page
date: 2025-04-04T17:12:39Z
lastmod: 2025-04-04T18:37:55Z
tags: [计算机,ubuntu]
---


# 思源笔记发布Github Page
# 思源笔记发布Github Page


　　​`前提：`​你已经拥有了一个GitHub page的个人网站，并且你的操作系统是ubuntu
　　​`前提：`​你已经拥有了一个GitHub page的个人网站，并且你的操作系统是ubuntu


# 一、修改思源笔记的导出选项，自动添加元元素
# 一、修改思源笔记的导出选项，自动添加元元素


​![image](assets/image-20250404171626-m6yy65b.png)​
​![image](assets/image-20250404171626-m6yy65b.png)​


　　‍
　　‍


# 二、编写ubuntu的bash文件进行md文件的转移
# 二、编写ubuntu的bash文件进行md文件的转移


```
```
#!/bin/bash
#!/bin/bash


# 配置
# 配置
source_dir="/home/willson/下载"
source_dir="/home/willson/下载"
target_dir="/home/video/repo/willsoncen.github/_posts/2025"
target_dir="/home/video/repo/willsoncen.github/_posts/2025"
#repo_path="/home/video/repo/willsoncen.github/"
#repo_path="/home/video/repo/willsoncen.github/"
default_layout="post"
default_layout="post"


# 监控目录
# 监控目录
while true; do
while true; do
  new_zip=$(find "$source_dir" -maxdepth 1 -type f -name "*.zip" -cmin -1)
  new_zip=$(find "$source_dir" -maxdepth 1 -type f -name "*.zip" -cmin -1)


  if [[ -n "$new_zip" ]]; then
  if [[ -n "$new_zip" ]]; then
    unzip "$new_zip" -d "$source_dir"
    unzip "$new_zip" -d "$source_dir"
    md_file=$(find "$source_dir" -maxdepth 1 -type f -name "*.md" -not -name "$(basename "$new_zip" | sed 's/\.zip$//').md")
    md_file=$(find "$source_dir" -maxdepth 1 -type f -name "*.md" -not -name "$(basename "$new_zip" | sed 's/\.zip$//').md")


    if [[ -n "$md_file" ]]; then
    if [[ -n "$md_file" ]]; then
      timestamp=$(date +"%Y-%m-%d")
      timestamp=$(date +"%Y-%m-%d")
      filename_without_ext=$(basename "$md_file" .md)
      filename_without_ext=$(basename "$md_file" .md)
      new_filename="${timestamp}-${filename_without_ext}.md"
      new_filename="${timestamp}-${filename_without_ext}.md"
      new_path="${target_dir}/${new_filename}"
      new_path="${target_dir}/${new_filename}"


      # 读取思源笔记导出的元数据
      # 读取思源笔记导出的元数据
      sy_title=$(grep "^title: " "$md_file" | sed 's/^title: //')
      sy_title=$(grep "^title: " "$md_file" | sed 's/^title: //')
      sy_tags_raw=$(grep "^tags: \[" "$md_file" | sed 's/^tags: \[\(.*\)\]/\1/' | sed 's/ //g') # 获取原始标签字符串，去除空格
      sy_tags_raw=$(grep "^tags: \[" "$md_file" | sed 's/^tags: \[\(.*\)\]/\1/' | sed 's/ //g') # 获取原始标签字符串，去除空格
      sy_tags=$(echo "$sy_tags_raw" | sed 's/,/, /g') # 添加标签之间的空格，更符合Jekyll习惯
      sy_tags=$(echo "$sy_tags_raw" | sed 's/,/, /g') # 添加标签之间的空格，更符合Jekyll习惯
      sy_date_raw=$(grep "^date: " "$md_file" | sed 's/^date: //')
      sy_date_raw=$(grep "^date: " "$md_file" | sed 's/^date: //')
      sy_date=$(echo "$sy_date_raw" | cut -dT -f1)
      sy_date=$(echo "$sy_date_raw" | cut -dT -f1)


      # 构建 Jekyll 元数据
      # 构建 Jekyll 元数据
      jekyll_metadata="---
      jekyll_metadata="---
layout: ${default_layout}
layout: ${default_layout}
title: ${sy_title}
title: ${sy_title}
tags: [${sy_tags}]
tags: [${sy_tags}]
date: ${sy_date:-$(date +"%Y-%m-%d")}
date: ${sy_date:-$(date +"%Y-%m-%d")}
---
"

      # 读取 Markdown 文件内容（跳过思源笔记的元数据块）
      content=$(sed '/^---$/,/^---$/!p' "$md_file")

      # 写入新的带有 Jekyll 元数据的 Markdown 文件
      echo -e "$jekyll_metadata
$content" > "$new_path"

      # Git 操作
      #cd "$repo_path"
      #git add "$new_path"
      #git commit -m "Add new post: ${sy_title}"
      #git push origin main

      echo "Processed: ${new_filename}"
      rm "$md_file" # 删除原始的思源笔记导出的 Markdown 文件
    fi
    rm "$new_zip"
  fi
  sleep 60
done
```
