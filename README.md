# Lets-Defend-Incident-Response-6

**Passwd Found in Requested URL - Possible LFI Attack**

**Summary:**

In this incident I was alertted by WebServer1006, "URL Contains passwd". The Requested URL in question was "https://172.16.17.13/?file=../../../../etc/passwd". Checking the Endpoint logs, this request was the only one to the network from the malicous IP 106.55.45.162. The HTTP response status was 500 and size of 0 indicated this LFI attack was unsuccessful. No other traffic was sent from this IP, no server was infected and escalation is not needed.

**Incident Response:**

I start by selecting `>>` to create a case and take ownership.

<img width="710" alt="1" src="https://github.com/user-attachments/assets/5fa2df9c-b013-4c3d-8a06-a6040ff7ec24">

Then I start then playbook.

<img width="593" alt="1" src="https://github.com/user-attachments/assets/6bd0a9c5-6417-4749-9b02-c2bba3cbd4c9">

Im showed the following page.

<img width="518" alt="Screenshot 2024-11-21 095749" src="https://github.com/user-attachments/assets/28aea22d-b700-4dba-9eca-b1a7a43dfd2c">

I'll start my investigation by noting down all the data I am provided with through the SIEM.

<img width="644" alt="Screenshot 2024-11-21 095914" src="https://github.com/user-attachments/assets/2f3cd78c-7cbe-4c83-9dec-0cbe26dd5503">

I can see why the alert was triggered: `"URL Contains passwd"`

I can also see the exact request URL that was flagged: `https://172.16.17.13/?file=../../../../etc/passwd`

This is a concerning request. the .. is selecting to go to the parent file and trying to get the password in the etc folder.

I also see the exact endpoint affected by this: `WebServer1006`

I'll start by seeing who the source IP is. I first check if the source is in network.

<img width="186" alt="Screenshot 2024-11-21 100502" src="https://github.com/user-attachments/assets/8ed2d3e3-711b-4f91-991e-f3224a9b3224">

Nothing found.

Next I check any data flow from the source in the `Log Managment` tab.

<img width="698" alt="Screenshot 2024-11-21 100620" src="https://github.com/user-attachments/assets/71fabb4e-4ccb-40ab-be6e-100d4cbdf87c">

I see 1 entry. And it matches this incident.

<img width="275" alt="Screenshot 2024-11-21 100652" src="https://github.com/user-attachments/assets/b6e1e505-f32f-4c0f-87f2-bf8522524ae7">

<img width="429" alt="Screenshot 2024-11-21 100854" src="https://github.com/user-attachments/assets/78d02346-de68-4dc6-bd4e-19dbdd077fc1">

Scrolling down the `HTTP response code: 500` is a good sign.

An HTTP response code of 500, also known as an "Internal Server Error", indicates that the server encountered an unexpected condition that prevented it from fulfilling the request:

This shows this attack as unsuccesful!

Continuing with the play book.

<img width="503" alt="Screenshot 2024-11-21 100956" src="https://github.com/user-attachments/assets/bd547d1f-f1a4-409e-8fb9-b30b1cc14169">

To collect this data I will be using `VirusTotal` and `Cisco Talos`.

<img width="942" alt="Screenshot 2024-11-21 101204" src="https://github.com/user-attachments/assets/c36e23ff-9ce5-4af8-92aa-808e5029e00c">
<img width="504" alt="Screenshot 2024-11-21 101220" src="https://github.com/user-attachments/assets/7720b5bc-a00e-40d2-a56c-84d0b987f157">
<img width="359" alt="Screenshot 2024-11-21 101336" src="https://github.com/user-attachments/assets/2115eb33-dfa1-469e-a51d-da6bdbd578d9">
<img width="907" alt="Screenshot 2024-11-21 101422" src="https://github.com/user-attachments/assets/9654cd7e-0152-49ed-b2fa-d4f554f4c1f9">

Ownership of the IP addresses and devices.
* Source - 106.55.45.162 - 	tencent cloud computing beijing co. ltd.
* Destination - 172.16.17.13 - WebServer1006

If the traffic is coming from outside (Internet);
* Yes we can see it is coming from China, which is worrying.

Ownership of IP address (Static or Pool Address? Who owns it? Is it web hosting?)
* Static - tencent cloud computing beijing co. ltd.

Reputation of IP Address (Search in VirusTotal, AbuseIPDB, Cisco Talos)
* The reputation given was poor and neutral.

<img width="517" alt="Screenshot 2024-11-21 101736" src="https://github.com/user-attachments/assets/d4cf4e0d-2a01-4429-9fdb-d2313446d6d2">

* Yes this traffic was intended to be malicous and export passwords.

<img width="498" alt="Screenshot 2024-11-21 101831" src="https://github.com/user-attachments/assets/5439ba69-82dd-47f2-a398-9a83ac3fd8c4">

* From the given command of `https://172.16.17.13/?file=../../../../etc/passwd` this makes it very obvious it is a (LFI) Local File Inclusion by trying to press multiple folders.

<img width="510" alt="Screenshot 2024-11-21 102036" src="https://github.com/user-attachments/assets/11d47d14-2b0f-403a-bb6d-0d8054910efa">

To check if it wads planned I can look inside the `Email Security` tab and filtering by either recipiant (WebServer1006 or source IP).

Nothing was found so it was not planned.

<img width="339" alt="Screenshot 2024-11-21 102236" src="https://github.com/user-attachments/assets/dfc5d0a4-d7a2-4a8d-96fa-d9b7a3556c34">

<img width="608" alt="Screenshot 2024-11-21 102245" src="https://github.com/user-attachments/assets/d8c85d21-2c39-4582-b2db-25e337d21dca">

Next is the direction of the traffic.

<img width="477" alt="Screenshot 2024-11-21 102347" src="https://github.com/user-attachments/assets/9a5ed544-7854-44a0-b177-53c055c776b5">

* As noted previously the source is from China so it will be `Internet -> Company Network`

<img width="527" alt="Screenshot 2024-11-21 102509" src="https://github.com/user-attachments/assets/29c305ff-96d7-4f5e-9efd-8b24de63dffc">

* Checking the `HTTP response status`

<img width="429" alt="Screenshot 2024-11-21 100854" src="https://github.com/user-attachments/assets/a3f70ee8-bebc-405e-bac9-3c0ca3d2bdf4">

* With the code 500 I can prove it was unsuccessful.

<img width="497" alt="Screenshot 2024-11-21 102827" src="https://github.com/user-attachments/assets/48e741b7-f13a-4dad-9af1-fd17ef51cf05">

* I add my artifacts.

<img width="518" alt="Screenshot 2024-11-21 102849" src="https://github.com/user-attachments/assets/be57eb92-0c6b-493c-9e67-bd1b58addfe4">

* This incident wont need escalation due to the HTTP response status 500.

<img width="531" alt="Screenshot 2024-11-21 103317" src="https://github.com/user-attachments/assets/cbf18f8e-b9c1-4b7f-b0b9-bdababa6bb43">

* Add breif notes to describe what was done.

* By mistake I chose false positive. But this incident was a true positive, but an unsuccessful true positive.

<img width="626" alt="Screenshot 2024-11-21 103448" src="https://github.com/user-attachments/assets/0340e305-f9d1-40bc-942f-37310d99f6c3">

