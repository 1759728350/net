
```python
import os

folder_path = "/path/to/folder" # 替换成你的文件夹路径

# 遍历文件夹
for item in os.listdir(folder_path):
    item_path = os.path.join(folder_path, item)
    if os.path.isfile(item_path): # 判断是否为文件
        print("File:", item)
    elif os.path.isdir(item_path): # 判断是否为文件夹
        print("Folder:", item)
```

在这个示例代码中，我们首先指定了要遍历的文件夹路径，然后使用`os.listdir()`函数获取文件夹中的所有文件和文件夹。对于每个文件或文件夹，我们使用`os.path.join()`函数获得完整路径，并使用`os.path.isfile()`和`os.path.isdir()`函数判断类型并打印出来。