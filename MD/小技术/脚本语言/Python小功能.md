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

  

### 将网页转换成pdf

```python
# 导入库
import flask
import pdfkit
import json
import base64
import time
from flask import Response, request

server = flask.Flask(__name__)

options = {
    'page-size': 'Letter',
    'margin-top': '0.75in',
    'margin-right': '0.75in',
    'margin-bottom': '0.75in',
    'margin-left': '0.75in',
    'encoding': "UTF-8",
    'no-outline': None
}


@server.route('/transToPdf', methods=['get', 'post'])
def trans_pdf():
    # 入参form_data
    url = flask.request.values.get('url')
    # 命名文件名
    to_file = 'out_1.pdf'

    # 将wkhtmltopdf.exe程序绝对路径传入config对象
    path_wkthmltopdf = r'/usr/local/bin/wkhtmltopdf'
    config = pdfkit.configuration(wkhtmltopdf=path_wkthmltopdf)
    # 生成pdf文件，to_file为文件路径
    # pdfkit.from_url(url, to_file, configuration=config, options=options)
    pdf = pdfkit.from_url(url, False)

    data = "转换成功"

    return Response(json.dumps(data), mimetype='application/json')


if __name__ == '__main__':
    # url = 'https://mp.weixin.qq.com/s/s6YxldWy8mVtPDkrgs4-tw'
    # filename = 'out_1.pdf'
    server.run(port=8898, debug=True, host='0.0.0.0', threaded=True)

```



  

