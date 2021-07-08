Skip to content
Why GitHub? 
Team
Enterprise
Explore 
Marketplace
Pricing 
Search

Sign in
Sign up
maclermo
/
bs-rtxbot
00
Code
Issues
Pull requests
Actions
Projects
Wiki
Security
Insights
bs-rtxbot/main.py

Maxime Clermont first commit
Latest commit 0c14512 24 minutes ago
 History
 0 contributors
101 lines (87 sloc)  3.84 KB
  
import re
import requests
from bs4 import BeautifulSoup

NEWEGG_URLS = [
    "https://www.newegg.ca/msi-geforce-rtx-3080-rtx-3080-gaming-x-trio-10g/p/N82E16814137597?Description=rtx%203080&cm_re=rtx_3080-_-14-137-597-_-Product",
    "https://www.newegg.ca/msi-geforce-rtx-3080-rtx3080-suprim-x-10g/p/N82E16814137609?Description=rtx%203080&cm_re=rtx_3080-_-14-137-609-_-Product",
]
BESTBUY_URLS = [
    "https://www.bestbuy.ca/fr-ca/produit/carte-graphique-nvidia-geforce-rtx-3080-gaming-x-trio-a-memoire-gddr6x-de-10-go-de-msi/15459505",
]
CANCOMP_URLS = [
    "https://www.canadacomputers.com/product_info.php?cPath=43_557_559&item_id=181348",
    "https://www.canadacomputers.com/product_info.php?cPath=43_557_559&item_id=185084",
]
DIRECTDIAL_URLS = [
    "https://www.directdial.com/ca/fr/item/msi-nvidia-geforce-rtx-3080-graphic-card/g3080gxt10",
]

HEADERS = {
    "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:89.0) Gecko/20100101 Firefox/89.0",
    "Connection": "keep-alive",
    "Accept": "*/*",
}
TIMEOUT = 5


def print_url(url, offset=50):
    return "{}...{}".format(url[:offset], url[-offset:])


def newegg(urls):
    for url in urls:
        try:
            site = requests.get(url, timeout=TIMEOUT, headers=HEADERS)
            bs = BeautifulSoup(site.content, "html.parser")
            div = bs.find_all("div", class_="product-inventory")
            dirty_result = [str(d) for d in div][0]
            result = " ".join(dirty_result.split())
            if re.match("(?:.*)OUT OF STOCK(?:.*)", result):
                print("No stock 😞 : {}".format(print_url(url)))
            else:
                print("In stock 😃 : {}".format(print_url(url)))
        except:
            print("Cannot connect to {}".format(url))


def bestbuy(urls):
    for url in urls:
        try:
            site = requests.get(url, timeout=TIMEOUT, headers=HEADERS)
            bs = BeautifulSoup(site.content, "html.parser")
            div = bs.find_all("button", class_="button_2m0Gt")
            dirty_result = [str(d) for d in div][0]
            result = " ".join(dirty_result.split())
            if re.match("(?:.*)disabled_LqxUL(?:.*)", result):
                print("No stock 😞 : {}".format(print_url(url)))
            else:
                print("In stock 😃 : {}".format(print_url(url)))
        except:
            print("Cannot connect to {}".format(url))


def cancomp(urls):
    for url in urls:
        try:
            site = requests.get(url, timeout=TIMEOUT, headers=HEADERS)
            bs = BeautifulSoup(site.content, "html.parser")
            div = bs.find_all("div", class_="pi-prod-availability")
            dirty_result = [str(d) for d in div][0]
            result = " ".join(dirty_result.split())
            if re.match("(?:.*)Online In Stock(?:.*)", result):
                print("In Stock Online 😃 : {}".format(print_url(url)))
            elif re.match("(?:.*)Available In Stores(?:.*)", result):
                print("In Stock In-Store 😃 : {}".format(print_url(url)))
            else:
                print("No stock 😞 : {}".format(print_url(url)))
        except:
            print("Cannot connect to {}".format(url))


def directdial(urls):
    for url in urls:
        try:
            site = requests.get(url, timeout=TIMEOUT, headers=HEADERS)
            bs = BeautifulSoup(site.content, "html.parser")
            div = bs.find_all("div", class_="availability")
            dirty_result = [str(d) for d in div][0]
            result = " ".join(dirty_result.split())
            if re.match("(?:.*)Backordered(?:.*)", result):
                print("No stock 😞 : {}".format(print_url(url)))
            else:
                print("In stock 😃 : {}".format(print_url(url)))
        except:
            print("Cannot connect to {}".format(url))


newegg(NEWEGG_URLS)
bestbuy(BESTBUY_URLS)
cancomp(CANCOMP_URLS)
directdial(DIRECTDIAL_URLS)
© 2021 GitHub, Inc.
Terms
Privacy
Security
Status
Docs
Contact GitHub
Pricing
API
Training
Blog
About
