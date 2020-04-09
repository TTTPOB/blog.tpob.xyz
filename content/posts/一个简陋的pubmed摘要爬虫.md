---
title: 一个简陋的pubmed摘要爬虫
date: 2018-12-02 16:29:35
tags:
---
# 一个简陋的pubmed摘要爬虫

前段时间被组里布置了一个很烦人的任务，要看很多文献的摘要提取这些研究的结果，我觉得用手点实在是太麻烦了，于是想用爬虫来完成copy摘要的任务。虽然我还不会写爬虫（甚至没怎么写过python），但是都可以学的嘛。  
<!-- more -->
直接上代码，这个代码非常不pythonic，但是你要考虑到我能用就行的标准不是？：
```python
#!/home/tpob/miniconda3/bin/python
import requests, lxml, urllib, re
from bs4 import BeautifulSoup
# 定义UA， 虽然pubmed好像根本没有反爬虫措施
headers = {
    "User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.110 Safari/537.36"
}
# 读进我要查的文献列表，每行一个
with open('/home/tpob/paper_list.txt', 'r') as paper_list:
    paper_names=paper_list.read().splitlines()
# 等一会BeautifulSoup解析出来的元素都是标签，用这个函数方便后面map成plain text
def tag_to_string(tag):
    return tag.text
# 开文件准备往进写
with open('/home/tpob/abstracts.md', 'a') as abstracts_file:
    print("# Abstracts of all papers:\n", file=abstracts_file)
    for paper_name in paper_names:
        paper_name_url = {
            "term":paper_name.encode("utf-8")
        }
        pubmed_request = requests.get("https://www.ncbi.nlm.nih.gov/pubmed/", headers = headers, params = urllib.parse.urlencode(paper_name_url))
        pubmed = BeautifulSoup(pubmed_request.text, 'lxml')
        try:
        # 下面这句的ref=re.compile......是为了防止跳进comments
            paper_id = pubmed.find("a", href = re.compile("^/pubmed/\d+"), ref=re.compile("citationsensor$"))['href']
        # 这个异常写成这个鬼样子因为我发现如果你查的文献在pubmed上只有一个匹配就会直接跳过去，而不是像下面会得到搜索结果页面，然后再点第一个。所以在这里我就直接跳过下面从搜索结果里找的步骤了。（居然没有报错，真是神奇(其实错了，但是现在是修正过的版本)）
        except:
            paper_content = pubmed_request
        else:
            paper_content = requests.get("https://www.ncbi.nlm.nih.gov"+paper_id)
        paper_content = BeautifulSoup(paper_content.text, 'lxml')
        # 同样的你可以f12
        journal_name=paper_content.find('a', href = '#', class_='')['title']
        paper_abstract = paper_content.find('div', class_ = 'abstr')
        pmid = paper_content.find_all('dd', text=re.compile("^\d{8}"))[0].text
        if paper_abstract!=None:
            abstract_sub_title=paper_abstract.find_all('h4')
            abstract_text=paper_abstract.find_all('p')
            print(paper_name)
            abstract_for_human = ''
            abstract_for_human += "## "+paper_content.find('h1', class_='').text+"\n"
            # 我知道这行代码非常不pythonic不要骂我我才刚开始学。。。然后这个的目的是，NCBI上的abstract很多是有小标题的，这样我就把每个小标题分开，得到human readable的abstract
            if len(abstract_sub_title)>0:
                for i in range(len(abstract_sub_title)-1):
                    abstract_for_human+="### "+abstract_sub_title[i].text+"\n"
                    abstract_for_human+=abstract_text[i].text+"\n"
            # 当然，也有很多没有小标题，底下那个map就是上面说的把标签转文字的作用啦不然没法join，list又不能直接.text
            else:
                abstract_for_human+="\n".join(list(map(tag_to_string ,abstract_text)))
            print(abstract_for_human+"\n", file=abstracts_file)
            print("[PMID:%(pmid)s](https://www.ncbi.nlm.nih.gov/pubmed/%(pmid)s)\n\n"%{'pmid':pmid}, file=abstracts_file)
            print("[Journal: %(journal)s]\n"%{'journal':journal_name}, file=abstracts_file)

        # 你说气不气，有的连摘要都没有（后来发现是我错了）
        else:
            print("## \"%s\" has no abstract!\n" % paper_name, file=abstracts_file)
            print("[PMID:%(pmid)s](https://www.ncbi.nlm.nih.gov/pubmed/%(pmid)s)\n\n"%{'pmid':pmid}, file=abstracts_file)
            print("[Journal: %(journal)s]\n"%{'journal':journal_name}, file=abstracts_file)
```
好啦这个简陋的爬虫就这么几行的代码，希望大家给我提一些改进意见加一些新功能比如查影响因子什么的（如果真的有人看的话）。