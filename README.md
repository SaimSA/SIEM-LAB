# SIEM-LAB
The purpose of this lab was to gain some exposure on SIEM’s. The setup involved a honeypot VM in the cloud and using Azure Sentinel to monitor attacks and failed RDP logs from all over the world to my VM. The locations of attackers were sent into Sentinel and illustrated on a world map. Josh Madakor’s SIEM Tutorial was followed.
After setting up my Azure subscription, I was presented with  the Azure portal. From there I went to the Virtual Machines section and created the Virtual Machine that will be exposed to everyone. I made sure to create a network security group that allowed everything into the VM.
![image](https://user-images.githubusercontent.com/131204778/233865783-6bb56155-9c28-4a1d-8700-ec6191ee74fc.png)

I then went to the “Log Analystics Workspaces” section to create a Log Analytics Workspace. This stores the logs created when there are failed attempts to the VM.
![image](https://user-images.githubusercontent.com/131204778/233865921-73768f73-cb87-4e06-8eca-650c1e82afcb.png)


After that, I went to the “Microsft Defender for Cloud” section to allow log transfering from the VM to the Log Analytics Workspace. 

From there, I went back to Log Analytics Workspace to connect it to the VM. While that was loading, I set up Azure Sentinel. 
![image](https://user-images.githubusercontent.com/131204778/233865936-5226e26e-5cea-4130-8130-be0b975ad87a.png)


There were some things needed to be set up in the VM so I connected to it via Remote Desktop.
![image](https://user-images.githubusercontent.com/131204778/233865947-145077cd-ae8a-4796-a8f2-69159e523fd1.png)


I turned the the Windows Defender Domain profile and Private profile off to allow more discoverability. 
![image](https://user-images.githubusercontent.com/131204778/233865956-722b9ea6-18fa-49a2-954b-2e26755613e3.png)


To export the security logs, I used Josh Madakor’s custom security log exporter PowerShell script from https://github.com/joshmadakor1/Sentinel-Lab
![image](https://user-images.githubusercontent.com/131204778/233865975-1420dc5e-eea7-4af3-bcdd-ecb7f9d8108d.png)

I copied the code, opened PowerShell ISE and pasted it as a new script. The script required me to add in my own API key for ipgeolocation.io. This is what is used for getting the latitude and longitude of those trying to access the VM.
![image](https://user-images.githubusercontent.com/131204778/233866038-4ab2fd00-3c53-4c25-b38f-0b629f07c01f.png)


Once it is running, it looks for updates in the security logs, sends the IP addresses to the API, gets the country geodata, and creates the failed logon log file.

Numerous login attempts started coming in and appearing on PowerShell!
![image](https://user-images.githubusercontent.com/131204778/233866049-7214fa76-3b25-4b2f-9237-a6a7f30ca2bf.png)


I had to create a custom log inside Log Analytics Workspace, enabling me to bring the custom log generated by the PowerShell script.
![image](https://user-images.githubusercontent.com/131204778/233866058-7c05691c-c72f-4cc2-906d-d78379c093bd.png)
![image](https://user-images.githubusercontent.com/131204778/233866060-39a83432-e36c-4114-92ed-3c10033d4a78.png)
![image](https://user-images.githubusercontent.com/131204778/233866066-e0373371-e3c8-43a9-9ec4-8de73da999e8.png)

After running this log as a query, the logs appeared to be successfully transferred.
![image](https://user-images.githubusercontent.com/131204778/233866074-14c562bc-746e-48e1-b9a7-a85af896f580.png)

I picked one of these logs and labeled each section including latitude, longitude, state, username etc., to train the extract process.
![image](https://user-images.githubusercontent.com/131204778/233866082-fefec7a8-c6af-4e0f-bea5-8d98c964046d.png)
![image](https://user-images.githubusercontent.com/131204778/233866093-1f7e2e40-9d25-4feb-9f01-cba79c73e425.png)


The logs are now extracted with sections.
![image](https://user-images.githubusercontent.com/131204778/233866107-406a40d4-7643-4efa-b50b-531dab328911.png)

While it was picking up logs, I set up the World Map by adding a workbook in Sentinel.
![image](https://user-images.githubusercontent.com/131204778/233866120-f36ca7ed-90a1-4daf-9df3-00094bb3e1f2.png)
![image](https://user-images.githubusercontent.com/131204778/233866124-6eea03a5-28e3-49b1-a643-b427a9962d2e.png)

I excluded test logs with destination hosts set as “samplehost”, and blank logs with no sourcehost.
I was then able to visualise the results on a map.
![image](https://user-images.githubusercontent.com/131204778/233866134-1f93d433-bff2-4c39-82f5-7e8a5d5657c0.png)

I plotted the latitudes and longitudes on the map.

![image](https://user-images.githubusercontent.com/131204778/233866139-e0aef249-96d2-4f7e-a68b-4037e04384f8.png)

The map was now set up and ready to go. It refreshed itself every 5 minutes and plotted new areas ever so often. The first attempts came from Vietnam. I left it on overnight to see how populated it would get.
![image](https://user-images.githubusercontent.com/131204778/233866146-e3c500e3-c7f4-4098-ace6-4df8d3746a22.png)
![image](https://user-images.githubusercontent.com/131204778/233866156-5bf7873d-be99-4a96-8bae-d81f69a37804.png)

The next day there were many more attacks from all over the world. The one from Vietnam had spent all night going through 22.7 thousand username and password combinations. From that point, I decided to delete the VM and conclude the lab as I felt the map was populated enough. 
![image](https://user-images.githubusercontent.com/131204778/233866179-fa936ab3-28cf-4789-ab7e-47fe56f603d1.png)

This lab was very useful in giving me experience in the use of PowerShell scripts to extract Windows logs, Azure Log Analytics Workspace, and Azure Sentinel so special thanks to Josh Madakor for the tutorial. https://www.youtube.com/watch?v=RoZeVbbZ0o0&t=141s&pp=ygURam9zaCBtYWRha29yIHNpZW0%3D



