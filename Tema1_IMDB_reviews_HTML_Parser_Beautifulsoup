import requests
import bs4
import re
from tqdm import tqdm
import pandas as pd
import pdb
import json


def parse_html(html):
    return bs4.BeautifulSoup(html, "html.parser")

def get_number_of_movies():
    html = requests.get(f"https://www.imdb.com/chart/top/").content
    soup = parse_html(html)

    submission_count_text = soup.find("div", {"class": "desc"}).text
    print(submission_count_text)
    submission_count = int(re.search(r"\d+", submission_count_text).group())
    print(submission_count)

def get_movie_link(title):
    html=f"https://www.imdb.com{title}"
    return html


html = requests.get(f"https://www.imdb.com/chart/top/").content
soup = parse_html(html)
lines = soup.select("tbody tr")
url=[]
for line in lines:
    cells = [cell for cell in line.select("td")]

    try:
        url_film= cells[1].select_one("a")["href"]
        url.append(url_film)
    except Exception:
        continue


def write_reviews(soup):
    ratings = soup.find_all("div", {"class": "review-container"})
    for rating in ratings:
        title=rating.find("a", {"class": "title"}).get_text().strip("\n \"")
        date=rating.find("span", {"class": "review-date"}).get_text()
        user = rating.find("span", {"class": "display-name-link"}).get_text()
        ratingContainer = rating.find("span", {"class": "rating-other-user-rating"})
        if ratingContainer != None:
            nota = ratingContainer.find('span').get_text()
        else:
            nota = None

        text = rating.find("div", {"class": "text show-more__control"}).get_text()
        list_of_reviews.append({"Movie Title":movie_title, "Review Title":title, "Nota":nota,"Date":date, "User":user, "review":text})


list_of_reviews=[]
nr=0
for url_film in url:
    nr+=1
    print(nr)
    html=requests.get(get_movie_link(url_film)+"reviews").content
    soup = parse_html(html)

    movie_title1 =soup.find("div", {"class": "parent"}).text.replace("\n","")
    list=movie_title1.split()
    movie_title=""
    for s in list:
        movie_title+=s+" "

    write_reviews(soup)
    for i in range(3):  # de cate ori vreau sa apas pe butonul de load more
        try:
            load_more_key=soup.find("div", {"class": "load-more-data"})["data-key"]
            load_more_url=get_movie_link(url_film)+"reviews"+f'/_ajax?ref_=undefined&paginationKey={load_more_key}'  #gasim link-ul html ului care este adaugat cand apasam load more
            html=requests.get(load_more_url).content
            soup=parse_html(html)
            write_reviews(soup)
        except:
            break

with open("example.json", "w") as f:
    json.dump(list_of_reviews, f, indent=4)






