---
title: "job site scraper 🔎"
date: 2021-12-01
summary: "Test"
tags: ["Personal", "Web Development", "Python", "Web Scraping"]
---

At the end of 2021, while searching for a new position, I wanted to be notified of new job postings at a few specific organizations. Unfortunately, these organizations didn't offer job alert subscriptions, they simply had web pages listing their openings.

I created two separate scripts -- one for each job site -- primarily because the pages had different structures that required unique scraping logic. A future improvement would be consolidating everything into a single script, but my solution accomplished what I needed: it scraped the job websites every morning and emailed me the job title, description, and a direct link to each position.

I've included the code for one of the scripts below. It didn't seem substantial enough to warrant creating a dedicated GitHub repo!

```python
import requests
import smtplib
import ssl
import datetime
import psycopg2
from bs4 import BeautifulSoup
from time import sleep


# This method logs in to Google SMTP server and sends an email.
# Accepts a string parameter for the email message.
def send_email(message, recipient):

    # Set up email connection
    port = smtp_port
    smtp_server = smtp_server_name
    sender_email = my_email
    receiver_email = recipient
    password = password

    # SSL context
    context = ssl.create_default_context()

    # Log in and send email
    with smtplib.SMTP_SSL(smtp_server, port, context=context) as server:
        server.login(sender_email, password)
        server.sendmail(sender_email, receiver_email, message)
        server.quit()

# Open a connection to the specified database and return that connection
def connect_to_db():

    db_connection = psycopg2.connect(
        host=host_name,
        port=port,
        database=database_name,
        user=username,
        password=password
    )

    return db_connection


# MAIN LOGIC #

# Current date
today = datetime.date.today()
print(">>> Looking up jobs for", today)

# List to hold job listings
job_listings = []

# Dictionary to hold jobs in memory
job_dict = {}

# Queries for staff jobs that contain the keywords
url_queries = ["https://www.somejobsite.com/postings/search?utf8=%E2%9C%93&query=network&query_v0_posted_at_date=&query_position_type_id%5B%5D=1&commit=Search",
               "https://www.somejobsite.com/postings/search?utf8=%E2%9C%93&query=cyber&query_v0_posted_at_date=&query_position_type_id%5B%5D=1&commit=Search",
               "https://www.somejobsite.com/postings/search?utf8=%E2%9C%93&query=AWS&query_v0_posted_at_date=&query_position_type_id%5B%5D=1&commit=Search",
               "https://www.somejobsite.com/postings/search?utf8=%E2%9C%93&query=cloud&query_v0_posted_at_date=&query_position_type_id%5B%5D=1&commit=Search"]

# Iterate through queries
for url_query in url_queries:

    # Use 'requests' to retrieve web page
    try:
        webpage = requests.get(url_query)
    except Exception as error:
        print("Failed to request URL", error)

        # Craft error email
        subject = "Jobs Update Error"
        message = "Subject: {} \n\nError requesting a URL. See log.".format(subject)
        recipient_email = my_email
        send_email(message, recipient_email)

        # Stop running script
        exit()

    # Create BeautifulSoup html parser object
    bsoup = BeautifulSoup(webpage.content, "html.parser")

    # Get the section of the page that has the job listings
    job_listings += bsoup.find_all("div", class_="job-item job-item-posting")

    # Iterate through the job listings
    for job_listing in job_listings:

        # Retrieve job title from: <div class="job-item job-item-posting" data-posting-title="Communications Technician">
        job_title = job_listing.get('data-posting-title')

        # Retrieve job description from: <span class="job-description">
        job_description = job_listing.find("span", class_="job-description").text.strip()

        # Retrieve the job's links from: <span class="job-actions">
        job_urls = job_listing.find("span", class_="job-actions").find_all("a", href=True)

        # Extract the first link from the job urls and create the link for the job's page
        job_page = "https://www.somejobsite.com" + job_urls[0]["href"]

        # Retrieve the details of the job from: <div class="col-md-2 col-xs-12 job-title job-title-text-wrap col-md-push-2">
        job_details = job_listing.find_all("div", class_="col-md-2 col-xs-12 job-title job-title-text-wrap col-md-push-2")

        # Extract the associate job details
        posting_date = job_details[0].text.strip()
        job_number = int(job_details[2].text.strip())
        department = job_details[3].text.strip()

        # Add the job to the in-memory dictionary
        job_dict[job_number] = [job_title, posting_date, department, job_page, job_description]

    # Wait 20 seconds
    sleep(20)


# Check if job is old/new and update database accordingly
# Connect to database and create cursor
try:
    db_connection = connect_to_db()
    db_cursor = db_connection.cursor()

except (Exception, psycopg2.Error) as error:
    print("Failed to connect to database", error)

# Define the insert query
db_insert_query = """ INSERT INTO job_posting (N_JOB, D_ADDED, T_TITLE, D_POSTED, T_DEPARTMENT, U_PAGE, T_DESCRIPTION) VALUES (%s,%s,%s,%s,%s,%s,%s) """

# Define the query used to check if the record already exists
db_select_check_query = """ SELECT 1 FROM job_posting WHERE N_JOB = %s """

# Copy job dictionary to a separate list for emails
job_dict_to_email = job_dict.copy()

# Iterate through the scraped jobs
for job, job_details in job_dict.items():

    # Check if job already exists in the database - the brackets are
    db_cursor.execute(db_select_check_query, [job])
    db_row = db_cursor.fetchone()

    # If the record exists, remove the job listing from the dictionary to email
    if db_row is not None:

        # Remove job from the email dictionary
        job_dict_to_email.pop(job)

    # Else, add the record to the database
    else:
        # Assign job information to variables for columns
        n_job = job
        d_added = today
        t_title = job_details[0]
        d_posted = job_details[1]
        t_department = job_details[2]
        u_page = job_details[3]
        t_description = job_details[4]

        # Create insert record
        db_insert_record = (n_job, d_added, t_title, d_posted, t_department, u_page, t_description)

        # Insert the record
        try:
            db_cursor.execute(db_insert_query, db_insert_record)
            db_connection.commit()

        except (Exception, psycopg2.Error) as error:
            print("Failed to insert record", error)

# Disconnect from the database
db_connection.close()

# If the email dictionary has content, send email for remaining jobs
if job_dict_to_email:

    # Craft email
    subject = "Jobs Update " + str(today)
    recipient_email = my_email
    message = "Subject: {} \n\n".format(subject)

    # Build message from job dictionary
    for job, job_details in job_dict_to_email.items():

        title = job_details[0]
        posted = job_details[1]
        department = job_details[2]
        page_url = job_details[3]
        description = job_details[4]

        line = "[{}] for {}\n------------------------------\n{}\n\n{}\n\n\n".format(title, department, description, page_url)
        message += line

    # Create message content with subject and the newly crafted body
    message = message.encode("utf-8")

    # Call function to send email
    print("Sending email for", today, "with", len(job_dict_to_email), "job postings")
    send_email(message, recipient_email)
    print("Email sent")

# Else, do nothing
else:
    print("Nothing to email for", today)

```