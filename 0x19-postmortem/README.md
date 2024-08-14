postmortem is a process intended to help you learn from past incidents. It is a structured process for evaluating and documenting the details of a system outage, service disruption or incident. The primary purpose of a postmortem is to understand what has happened, why it has happened and how similar incidents can be prevented in the future.

Benefits of Postmortem

· Learning Opportunity: Postmortems provide a structured framework for teams to reflect on their actions and learn from mistakes. By analyzing incidents in detail, teams can identify gaps in their understanding, processes or systems and take steps to address them.

· Continuous Improvement: Postmortems facilitate a culture of continuous improvement by encouraging teams to identify areas for enhancement and implement corrective measures. By iteratively refining systems and processes, teams can enhance reliability and resilience over time.

· Prevention of Recurrence: By identifying and addressing root causes, postmortems help prevent the recurrence of similar incidents in the future. This proactive approach reduces the likelihood of service disruptions and minimizes the impact on users.

· Enhanced Collaboration: Postmortems foster collaboration among cross-functional teams by bringing together individuals with diverse expertise to analyze incidents collaboratively. By sharing insights and perspectives, teams can gain a deeper understanding of complex issues and develop more effective solutions.

· Transparency and Accountability: Postmortem promotes transparency and accountability by documenting incident details, analysis and actions taken. This transparency builds trust with stakeholders and demonstrates a commitment to reliability and excellence in service delivery.

Postmortems typically involve blame-free analysis and discussion soon after an incident or event has taken place. An artifact is produced that includes a detailed description of exactly what went wrong in order to cause the incident, along with a list of steps to take in order to prevent a similar incident from occurring again in the future. An analysis of how your incident response process itself worked during the incident should also be included in the discussion

Organizations may refer to the postmortem process in slightly different ways:

Learning Review
After-Action Review
Incident Review
Incident Report
Post-Incident Review
Root Cause Analysis (RCA)
Below is a postmortem outage sample:

Issue Summary On 14 July 2024, at 9:40 pm(EAT), the web server for our College went down due to security risk from running the Nginx web server as the root user and users were unable to use the college portal website.

Root Cause: The Nginx web server was being run by root, was replaced by another service and could no longer listen on port 8080.

Timeline

10:20 PM: Web stack engineers checked the currently running processes with ps auxff and find the Nginx web server was not running. They further investigated by running netstat -ldpn which gives them information about the processes running and their ports.

10:52 PM: Web stack engineers find apache2 is running on port 8080. They kill the process with pkill apache2 and use netstat -ldpn to confirm nothing is currently listening on port 8080.

11:26 PM: Engineers checked the /etc/nginx directory to examine Nginx-specific files. They find the nginx.conf configuration file is owned by the user and group root, instead of nginx like all other nginx files, and does not contain any read, write, or execute privileges.

11:39 PM: Engineers changed the owner and group to nginx with chown nginx:nginx nginx.conf and grant the user read, write, and execute privileges with chmod u+rwx nginx.conf.

11:46 PM: Engineers checked that Nginx is able to run by restarting the service using sudo service nginx restart. Nginx briefly run, but the site was listening on port 80 instead of port 8080.

11:50 PM: Engineers checked through common nginx files and find in /etc/nginx/sites-available/default that the server was default listening on port 80. This was changed to port 8080 and the file was saved.

11:53 PM: After restarting the Nginx service, engineers found nginx is listening on port 8080. One problem was fixed.

11:54 PM: However, when engineers checked the running systems again with ps auxff, they found that nginx was still running as root, which left the server more vulnerable to attack.

11:56 PM: Engineers stopped running the service with sudo or the root user. They then started the service with service nginx start after switching to the nginx user with su nginx.

11:57 PM: Nginx was running under the user nginx and listening on port 8080. The website for the College was back online and users can access the site.

Root Cause and Resolution

The root cause came from running the Nginx web server as the root user. This left the server vulnerable to attack and allowed a rival process, apache2, to take over. This attack also removed nginx user privileges and changed the default port the server was listening on.

Corrective and Preventative Measures

Run Nginx with a less-privileged user and NOT with root user
Check default settings in files
Add monitoring system
