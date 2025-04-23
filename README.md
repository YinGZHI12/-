
---

# 【爬虫学习笔记】

## 📖 入门篇：爬虫的本质与核心工具
### 1. 爬虫是什么？
爬虫是**自动化获取网页数据的程序**，核心是模拟浏览器行为。理解其本质后，会发现它就像一位不知疲倦的图书管理员，帮我们从海量网页中整理出目标信息。

### 2. HTTP协议基础
- 关键概念：`GET/POST`请求、状态码（200成功/404未找到/500服务器错误）
- 实用工具：Chrome开发者工具（Network标签页观察请求过程）

### 3. Requests库实战
```python
import requests

try:
    response = requests.get('https://example.com', timeout=5)
    print(f"状态码: {response.status_code}")
    print(f"网页长度: {len(response.text)}字节")
except Exception as e:
    print(f"请求失败: {str(e)}")
```

---

## 🔍 解析篇：数据提取的艺术
### 1. HTML解析基础
- BeautifulSoup的灵活应用
```python
from bs4 import BeautifulSoup

soup = BeautifulSoup(html_content, 'lxml')
title = soup.find('h1', class_='title').text
```

### 2. XPath精准定位
```python
from lxml import etree

tree = etree.HTML(html_content)
price = tree.xpath('//div[@id="price"]/text()')[0]
```

---

## 💾 数据存储篇：结构化你的战利品
| 存储方式 | 适用场景 | 示例库 |
|---------|----------|--------|
| CSV文件 | 快速存储表格数据 | csv |
| 数据库 | 结构化存储 | MySQL/MongoDB |
| JSON文件 | 嵌套数据结构 | json |

```python
# MongoDB存储示例
from pymongo import MongoClient

client = MongoClient('mongodb://localhost:27017/')
db = client['mydatabase']
collection.insert_one({'title': title, 'price': price})
```

---

## ⚔️ 挑战篇：突破反爬防线
### 反爬虫应对策略
1. **请求头伪装**：添加User-Agent
   ```python
   headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit...'}
   ```
2. **IP代理池**（防止封禁）
   ```python
   proxies = {'http': 'http://10.10.1.10:3128'}
   ```
3. **动态页面处理**：Selenium/Puppeteer应对JavaScript渲染

### 伦理红线
- 严格遵守`robots.txt`协议
- 控制请求频率（建议≥2秒/次）
- 禁止抓取敏感个人信息

---

## 🚀 总结与展望
### 学习收获
- 掌握了数据获取→解析→存储完整链路
- 构建了基础反爬应对能力
- 培养了自动化数据处理思维

### 未来方向
1. 进阶框架：Scrapy分布式爬虫
2. 智能解析：OCR验证码识别
3. 数据价值挖掘：结合pandas进行数据分析

> **学习感悟**：爬虫是打开数据世界大门的钥匙，但更需牢记——技术应当用于创造价值，而非突破法律边界。每一次数据采集前，请多问一句："这真的有必要吗？"
---
---
实例:
```python
import requests
from bs4 import BeautifulSoup
import time

def qidian_yuepiao_spider():
    # 基础配置
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/119.0.0.0 Safari/537.36',
        'Referer': 'https://www.qidian.com/'
    }
    
    try:
        # 发送请求
        response = requests.get(
            url='https://www.qidian.com/rank/yuepiao/',
            headers=headers,
            timeout=10
        )
        
        if response.status_code != 200:
            print(f'请求失败，状态码：{response.status_code}')
            return

        # 解析数据
        soup = BeautifulSoup(response.text, 'lxml')
        books = soup.select('.rank-view-list li')  # 根据实际页面结构调整选择器
        
        print(f'{"序号":<5}{"书名":<20}{"作者":<15}{"月票数":<10}{"类型":<10}')
        print('='*60)
        
        for index, book in enumerate(books[:20], 1):  # 取前20名
            try:
                title = book.select_one('.book-info-title').text.strip()
                author = book.select_one('.author a').text.strip()
                category = book.select_one('.author span').text.strip()
                # 月票数可能存在的不同位置
                votes = book.select_one('.month-ticket span').text.strip() \
                        or book.select_one('.vote-info').text.strip()
                
                print(f'{index:<5}{title[:15]:<20}{author[:10]:<15}{votes:<10}{category:<10}')
            except Exception as e:
                print(f"第{index}条数据解析失败：{str(e)}")
                continue

    except Exception as e:
        print(f'爬虫运行异常：{str(e)}')
    finally:
        # 遵守爬虫礼仪
        time.sleep(2)

if __name__ == '__main__':
    qidian_yuepiao_spider()
```
---


