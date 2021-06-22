## Python小功能

  

### 爬取网页文字信息

```python
import requests
from lxml import etree


def request_dandan(url):
    try:
        response = requests.get(url)
        if response.status_code == 200:
            return response.text
    except requests.RequestException:
        return None


def parse_result(url):
    resp = requests.get(url)
    text = resp.content.decode('utf-8')
#    time.sleep(2)

    # 提取数据
    html = etree.HTML(text)

    # /html/body/div[1]/div[2]/div[1]/div/div[1]/div[3]/p[1]/span
    # /html/body/div[1]/div[2]/div[1]/div/div[1]/div[3]/p[3]/span
    # /html/body/div[1]/div[2]/div[1]/div/div[1]/div[3]/section[13]/span
    # /html/body/div[1]/div[2]/div[1]/div/div[1]/div[3]/p[8]/span
    # nodes = html.xpath("/html/body/div[1]/div[2]/div[1]/div/div[1]/div[3]/")
    js_content = html.xpath("//div[@id='js_content']//text()")
    if js_content is not None:
        return js_content

    # 正则匹配模式不好用啊！！！
    # rex = 'id="js_content"(.*?)</span>'
    # pattern = re.compile(rex, re.S)
    # items = re.findall(pattern, html)
    #
    # for item in items:
    #     yield {
    #         'text': item[0],
    #     }


def write_item_to_file(item):
    print('开始写入数据 ====> ' + str(item))
    # ticks = int(round(time.time() * 1000))
    # filetime = time.strftime("%Y-%m-%d %H:%M:%S", time.localtime()) + ticks + 'file'
    filetime = 'book.txt'
    with open(filetime, 'a', encoding="utf-8") as f:
        f.write(str(item))


def main():
    url = 'https://mp.weixin.qq.com/s/s6YxldWy8mVtPDkrgs4-tw'
    items = parse_result(url)  # 解析过滤我们想要的信息
    if items is not None:
        for item in items:
            if str(item).strip() != "" and not str(item).strip().startswith("图源："):
                write_item_to_file(item)


if __name__ == '__main__':
    main()

```



---

  

### 将微信公众号链接文章转换成pdf

```python
__author__ = "XuFeiFan"
__date__ = "2021-06-22"

# 导入库
import datetime
import flask
import pdfkit
import json
import time
import wechatsogou
import requests

from flask import Response, request
from lxml import etree


server = flask.Flask(__name__)
# 格式初始化
options = {
    'page-size': 'Letter',
    'margin-top': '0.75in',
    'margin-right': '0.75in',
    'margin-bottom': '0.75in',
    'margin-left': '0.75in',
    'encoding': "UTF-8",
    'no-outline': None,
    'dpi': '96',
    'image-dpi': '600',
    'image-quality': '94'
}

# 初始化API
ws_api = wechatsogou.WechatSogouAPI(captcha_break_time=3)


@server.route('/trans2Pdf', methods=['get', 'post'])
def trans_pdf():
    # 入参form_data
    url = flask.request.values.get('url')

    # 命名文件名
    filename = datetime.datetime.now().strftime('%Y%m%d%H%M%S') + '.pdf'

    # 将wkhtmltopdf.exe程序绝对路径传入config对象
    path_wkthmltopdf = r'/usr/local/bin/wkhtmltopdf'
    time.sleep(10)
    config = pdfkit.configuration(wkhtmltopdf=path_wkthmltopdf)
    # 生成pdf文件，to_file为文件路径

    pdfkit.from_url(url, filename, configuration=config, options=options)
    # pdf = pdfkit.from_url(url, False)

    data = "转换成功"

    return Response(json.dumps(data), mimetype='application/json')


"""
将微信公众号链接转换为PDF保存
-------------------------
"""


@server.route('/url2pdf', methods=['get', 'post'])
def url2pdf():
    # 入参form_data
    url = flask.request.values.get('url')
    # 解析链接,返回文章内容，标题
    title, html, content_info = analysis(url)

    # 处理后的html
    html = f'''
        <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="UTF-8">
            <title>{title}</title>
        </head>
        <body>
        <h2 style="text-align: center;font-weight: 400;">{title}</h2>
        {content_info['content_html']}
        </body>
        </html>
    '''

    # target_path = ''
    # 部分文章标题含特殊字符，不能作为文件名
    filename = datetime.datetime.now().strftime('%Y%m%d%H%M%S') + '.pdf'
    try:
        pdfkit.from_string(html, filename)
    except:
        pdfkit.from_string(html, filename)

    data = "转换成功"
    return Response(json.dumps(data), mimetype='application/json')


"""
解析微信公众号链接url
-------------------
"""


def analysis(url):
    try:
        # 该方法根据文章url对html进行处理，使图片显示
        content_info = ws_api.get_article_content(url)

        # 文章标题
        html = requests.get(url).text
        con = etree.HTML(html)
        # 获取标题
        title = con.xpath('//h2[@class="rich_media_title"]/text()')[0]
    except:
        return False

    return title, html, content_info


"""
    此脚本版本要求 flask-0.11 Werkzeug-0.14.1 wechatsogou-4.5.4 否则出现不兼容
"""
if __name__ == '__main__':
    server.run(port=8898, debug=True, host='0.0.0.0', threaded=True)


```



  

