from selenium import webdriver
import bs4
import sqlite3
lower = int(input("enter lower limit"))
upper = int(input("enter upper limit"))
db = sqlite3.connect("workspace.sql")
db.execute("CREATE TABLE IF NOT EXISTS DUMMY(regno text,DWDM text,OOSE text,DAA text,CG text,CD text,CNS text,OOSE_LAB text,WT_LAB text,MOOCS text,SGPA text,CGPA text,REMARKS text)")
sql = "INSERT INTO DUMMY VALUES(?,?,?,?,?,?,?,?,?,?,?,?,?)"

for i in range(lower, upper+1):
    driver = webdriver.Chrome(executable_path='D:\chromedriver.exe')
    driver.get("https://aucoe.info/RDA/resultsnew/")
    old_window = driver.window_handles[0]
    sub = driver.find_element_by_id("res_filter")
    input = sub.find_element_by_tag_name("input")
    input.send_keys("B.TECH/INTEGRATED COURSE")
    driver.implicitly_wait(5)
    jamkay = driver.find_element_by_id("res")
    button = jamkay.find_element_by_tag_name("button")

    button.submit()

    new_window = driver.window_handles[0]
    driver.switch_to.window(new_window)
    sbox = driver.find_element_by_name("regno")
    sbox.send_keys(i)
    submit = driver.find_element_by_id("submit")
    submit.submit()
    soup = bs4.BeautifulSoup(driver.page_source, "html.parser")
    table = soup.find('table', width=1023, height=288, border=1)
    rows = table.find_all('tr')
    l = list()
    rowcount = 0
    for row in rows:
        if rowcount == 0:
            continue
        else:
            rowcount = rowcount+1
            data = row.find_all("td")
            l.append(data[1].get_text())
            db.execute(sql, i, l[0], l[1], l[2], l[3], l[4], l[5], l[6], l[7], l[8], l[9], l[10], l[11], l[12])
    driver.close()
db.commit()





#define a function to see all records of DUMMY table
