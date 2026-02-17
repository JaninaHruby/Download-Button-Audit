# Project: Data Analysis SaaS – Download Button Implementation Audit

### Background: A SaaS company had a high volume of support tickets and wanted to reduce costs by specifically decreasing inquiries about the product itself. During the analysis of these requests, it was noticed that many general questions about the product arose. As a result, a detailed manual was provided for download on the website.

### The Task: My task is to find out whether the introduction of the download actually led to a reduction in support requests.

### Information: To perform an unbiased EDA (Exploratory Data Analysis), I designed a synthetic dataset using Python (Faker module). The data structure was specifically modeled to reflect real-world business scenarios of a SaaS company. Naturally, the outcome of the analysis had to be incorporated into the generated data so that the scenario could be mapped as desired.

### Inspecting Data with Python
*Note: At this point, we have not yet addressed data manipulation in Python in the course. Therefore, only a brief induction is carried out here. I'll update the README as I go and post a summarized and appealing version of it at the end.*

### Project Progress & To-Do List:
- [x] Create synthetic dataset (Python Faker)
- [x] Initial data inspection (Pandas)
- [x] Set up PostgreSQL database & DBeaver environment
- [ ] Import CSV data into SQL tables
- [ ] Perform relational analysis (JOINs) to link User IDs and Tickets
- [ ] Analyze support ticket trends (Pre- vs. Post-Download)
- [ ] Final summary of results and business impact

# Loading the data
import pandas as pd

deployment_history = pd.read_csv("deployment_history_logs.csv")
raw_crm_customer_data = pd.read_csv("raw_crm_customer_data.csv")
sys_backend_error_logs = pd.read_csv("sys_backend_error_logs.csv")
web_tracking_event_log = pd.read_csv("web_tracking_event_log.csv")
zendesk_tickets_export = pd.read_csv("zendesk_tickets_export.csv")

# Now I will take a closer look at all CSVs
print(deployment_history)
print(raw_crm_customer_data)
Is the User Id listed anywhere else? Are there connections?
### The view in print is very cluttered

show_row_raw_customer = raw_crm_customer_data.iloc[0].to_frame()
print(show_row_raw_customer)

# Here I can see which channels the users come through and which OS is used. 
 print(sys_backend_error_logs.to_string)
 
### This all looks wrong somehow, maybe I'd better use something other than pure print?
show_row_sys_error = sys_backend_error_logs.iloc[0].to_frame()
print(show_row_sys_error)

# So there is an error message pointing to the pdf download. 

### Do the data provide a way to find out which User Id this happens to?
print(web_tracking_event_log.to_string)

show_row_web_tracking = web_tracking_event_log.iloc[0].to_frame()
print(show_row_web_tracking)

# Gotcha! 
### Here the session ID and the User ID are linked and I see how long the users were on the site, which could still be interesting
show_row_ticket = zendesk_tickets_export.iloc[0].to_frame()
print(show_row_ticket)

## How do I establish a connection between the User IDs now?
With pd.merge() but I don't think I'm doing that just yet

### Questions that have arisen:
- Are there more error logs after the release of the download button than before?
- How long do customers stay on the website before and after release? Are there differences between those with and without error messages?
- Have the tickets really decreased?
- When exactly does the error occur (OS, channel etc.)?

### Established that it makes sense to create a Postgres server and set up the database in DBeaver.
## Process successfully completed. Knowledge regarding DBeaver limited, follow-up: How do I use SQL there?
