import requests
import os
import re
import pandas as pd
import numpy as np
import time

# 代理IP
proxies = ["117.64.237.99:18118", "114.239.150.161:808",
           "36.25.43.10:9999", "163.204.245.113:9999",
           "114.239.151.194:808", "183.164.238.25:38393",
           "49.77.209.93:36999", "183.164.238.242:44004"]
           

def change_proxy():# 更换代理
    # 随机选择一个代理IP
    proxy = random.choice(proxies)
    print(proxy)
    if proxy == None:
        proxy_support = urllib.request.ProxyHandler({})
    else:
        proxy_support = urllib.request.ProxyHandler({'http': proxy})

    opener = urllib.request.build_opener(proxy_support)
    opener.addheaders = [
        ('User-Agent', 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.110 Safari/537.36')]
    urllib.request.install_opener(opener)

def needpages(page):#用来获取电影页数
    page_group = []
    for i in range(1, page+1):
        url = "https://www.dytt8.net/html/gndy/dyzz/list_23_1.html"
        link = re.sub('list_23_1', 'list_23_'+str(i), url)
        page_group.append(link)
    return page_group

def movielist(get_url):#获取电影列表url
    movieurls = []
    for i in get_url:
        req = requests.get(i)
        req.encoding = 'gb2312'
        content = req.text
        pattern = re.compile('<div class="title_all"><h1><font color=#008800>.*?</a>></font></h1></div>'+'(.*?)<td height="25" align="center" bgcolor="#F4FAE2">', re.S)
        items = re.findall(pattern, content)
        strs = ''.join(items)
        pattern2 = re.compile('<a href="(.*?)" class="ulink">.*?<td colspan.*?>', re.S)
        listurl = re.findall(pattern2, strs)
        for j in listurl:
            movieurls.append('https://www.dytt8.net'+j)
    return movieurls

def moviedetails(movieurls):#  获取电影详细信息
    name = []
    times = []
    doubanscore = []
    director = []
    labels = []
    introduction = []
    download = []

    for i in movieurls:
        req = requests.get(i)
        req.encoding = 'gb2312'
        content = req.text
        namepattern = re.compile('◎片　　名　(.*?)<br />', re.S)
        name.extend(re.findall(namepattern, content))

        timepattern = re.compile('◎片　　长　(.*?)<br />', re.S)
        times.extend(re.findall(timepattern, content))

        doubanpattern = re.compile('◎豆瓣评分\\u3000(.*?)from', re.S)
        doubanscore.extend(re.findall(doubanpattern, content))

        directorpattern = re._compile('◎导　　演　(.*?)<br />', re.S)
        director.extend(re.findall(directorpattern, content))

        labelspattern = re._compile('◎标　　签　(.*?)<br />', re.S)
        labels.extend(re.findall(labelspattern, content))

        idpattern = re._compile('◎简　　介[ ]*<br /><br />\\u3000\\u3000(.*?)<br />', re.S)
        introduction.extend(re.findall(idpattern, content))

        dlpattern = re._compile('<td style="WORD-WRAP: break-word" bgcolor="#fdfddf"><a href="(.*?)">', re.S)
        download.extend(re.findall(dlpattern, content))
        
        time.sleep(1)
        all = list(zip(name, times, doubanscore, director, labels, introduction, download))
    return all

if  __name__ == "__main__":
    page = input("请输入要爬取最新电影的页数:")
    get_url = needpages(int(page))
    movieurls = movielist(get_url)
    all = moviedetails(movieurls)
    movies = np.array(all, ndmin=2)
    if os.path.exists("movies.xlsx"):#创建文件
        os.remove("movies.xlsx")
    colum = ['片名', '片长', '评分', '导演', '标签', '简介', '下载链接']
    data = pd.DataFrame(movies, columns=colum)
    writer = pd.ExcelWriter('movies.xlsx')# 写入Excel文件
    data.to_excel(writer)	
    writer.save()
    writer.close()
    print("爬取成功！")
