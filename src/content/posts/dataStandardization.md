---
title: Data Standardization
published: 2025-01-16
description: This post demonstrates how to include embedded video in a blog post.
tags: [Python]
category: Python
draft: false
---

数据贯标思路.

```yaml
---
title: Data Standardization
published: 2025-01-16
// ...
---
```

```python
from collections import defaultdict
import pandas as pd
import re
import os

# 检查并删除原有的匹配结果文件
if os.path.exists('matched_results.xlsx'):
    os.remove('matched_results.xlsx')
    print("已删除原有的 'matched_results.xlsx' 文件")

# 读取两份Excel文件
df1 = pd.read_excel('C:/Users/mason/Desktop/新建文件夹/filtered_file.xlsx')
df2 = pd.read_excel('C:/Users/mason/Desktop/新建文件夹/test1.xlsx')

# 检查数据格式
print("第一份数据前5行：")
print(df1.head())

print("\n第二份数据前5行：")
print(df2.head())

# 定义一个函数，提取字符串中的中文部分
def extract_chinese(text):
    # 使用正则表达式匹配中文字符
    chinese_pattern = re.compile(r'[\u4e00-\u9fff]+')
    chinese_matches = chinese_pattern.findall(str(text))
    return ''.join(chinese_matches)  # 将所有匹配的中文拼接成一个字符串

# 调试：打印提取的中文部分
print("\n第一份数据中文提取示例：")
for index1, content in enumerate(df1.iloc[:, 0]):
    chinese_part = extract_chinese(content)
    print(f"原始: {content} -> 提取: {chinese_part}")

print("\n第二份数据中文提取示例：")
for index2, content in enumerate(df2.iloc[:, 0]):
    chinese_part = extract_chinese(content)
    print(f"原始: {content} -> 提取: {chinese_part}")

# 预处理第二份Excel的第一列数据，提取中文部分并构建字典
# 键为中文部分，值为对应的整行数据（pandas.Series）和行号
chinese_to_row = {}
for index2, row2 in df2.iterrows():
    chinese_part = extract_chinese(row2.iloc[0])  # 提取中文部分
    if chinese_part:  # 如果有中文部分
        chinese_to_row[chinese_part] = (row2, index2 + 1)  # 存储整行数据和行号（从1开始）

# 创建一个空的DataFrame来存储匹配结果
# 列名为第一份数据的列名 + 第二份数据的列名（添加后缀 '_matched'） + 匹配的行号
result_columns = list(df1.columns) + [col + '_matched' for col in df2.columns] + ['Matched_Row']
result = pd.DataFrame(columns=result_columns)

# 记录哪些行已经匹配成功，避免重复匹配
matched_indices = set()

# 第一步：完全匹配
for index1, row1 in df1.iterrows():
    chinese_name1 = extract_chinese(row1.iloc[0])  # 提取第一份数据的中文部分

    # 检查是否有完全匹配
    if chinese_name1 in chinese_to_row:
        matched_row2, matched_row_number = chinese_to_row[chinese_name1]
        # 将匹配结果添加到 result DataFrame
        new_row = row1.tolist() + matched_row2.tolist() + [matched_row_number]
        result.loc[len(result)] = new_row
        matched_indices.add(index1)  # 记录已匹配的行
        print(f"完全匹配成功: {chinese_name1} -> 第二份数据行号: {matched_row_number}")

# 第二步：对未匹配的行进行连续4个中文字符的模糊匹配
for index1, row1 in df1.iterrows():
    if index1 in matched_indices:  # 跳过已匹配的行
        continue

    chinese_name1 = extract_chinese(row1.iloc[0])  # 提取第一份数据的中文部分

    matched_row2 = None
    matched_row_number = None

    # 提取 chinese_name1 的所有连续4字符子串
    chinese_substrings = {chinese_name1[i:i+4] for i in range(len(chinese_name1) - 3)}

    # 遍历字典，检查是否有连续4个中文匹配
    for key in chinese_to_row:
        # 提取 key 的所有连续4字符子串
        key_substrings = {key[i:i+4] for i in range(len(key) - 3)}

        # 检查是否有共同的子串
        if chinese_substrings & key_substrings:  # 集合交集不为空
            matched_row2, matched_row_number = chinese_to_row[key]
            break

    # 如果找到匹配，将结果添加到 result DataFrame
    if matched_row2 is not None:
        new_row = row1.tolist() + matched_row2.tolist() + [matched_row_number]
        result.loc[len(result)] = new_row
        print(f"模糊匹配成功: {chinese_name1} -> 第二份数据行号: {matched_row_number}")
    else:
        print(f"未找到匹配: {chinese_name1}")

    # 打印进度（可选）
    if index1 % 100 == 0:
        print(f"Processed {index1 + 1} rows...")

# 将结果保存到新的Excel文件中
if not result.empty:
    result.to_excel('matched_results.xlsx', index=False)
    print("Matching completed! Results saved to 'matched_results.xlsx'.")
else:
    print("No matches found.")
```



