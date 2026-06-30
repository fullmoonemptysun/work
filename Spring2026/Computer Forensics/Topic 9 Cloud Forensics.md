## Cloud computing
- Computing resource able to be accesses using a network.
- Abstraction over computer hardware/OS/software.

## History of the cloud

## Cloud service model
- SaaS
- PaaS: OS installed on the server. users can install their tools
- IaaS: Hardware rental, Install OS of choice. 
![[Pasted image 20260505003206.png]]

## Locations of evidence
- SaaS: likely on end user  devices
- PaaS: Likely on desktop or server or on a company network.
- IaaS: Desktop or server

- Problems:
	- Service level agreements (SLAs)
	- Data storage may be located across the globe.

## Cloud deployment models
- Public cloud: Available to anyone
- Private cloud: Limited access (on premises)
- Community Cloud: Way to bring people together for a specific purpose.
- Hybrid Cloud: public and private cloud talk to each other


## Public Cloud:
- Open to general public
- Everything handled by the provider
- less control and privacy

## Private Cloud (corporate)
- Secure, customizable. Outside people can't access
- One specific company owns a private one.

## Community cloud
- share the infrastructure and related resources between clouds.

## Hybrid Cloud
- mix and match the aspects of all 3 as per required.


## Cyber crimes using the cloud
- Cloud assisted:
	- using cloud vms as bots or command and control servers
	- Data breach (tool)
- Cloud targeted:
	- against a cloud
	- policy violations in accessing a cloud
	- data breach (victim)
- Cloud incidental:
	- Fraud
	- Data breach (storage)


## Use cases of cyber crimes
- Cloud as phishing platforms
- Mounts for attacks like DoS (distributed) (botnets)
- Exposing and stealing sensitive corporate data

## Legal Challenges
- SLAs:  Among other things, these state who is authorized to access data and what the limitations are in conducting acquisitions for an investigation.
- Jurisdiction location issues
- Accessibility

## Technical Challenges
- Data Accessibility:
	- Remote storage limits physical access.
	- Volatile evidence is lost when instances terminate.
	- Multi-tenancy complicates data isolation.

- Data volume and Complexity:
	- Enormous Datasets make targeted collection difficult. 
	- Replication increases complexity

- Logs and metadata:
	- Logs may be incomplete or unavailable.
	- Proprietary formats require specialized tools.
	- Encryption restricts access to evidence.
	
- Deleted or terminated resources may result in permanent data loss if not captured promptly. (ephimeral environments)

- Costs and resource intensity
- Data integrity


## Timestamp considerations in cloud logs
- Time zone sync: Always convert from UTC to match real-world actions and log entries
- Event latency (occurence vs logging)
- Millisecond precision (Epoch time)
- Clock skew and drift: If a computer's clock is wrong, local logs will be out of sync with server side events. 


## Levels of investigation
- Cloud service provider (CSP):
	- Requires in-depth understanding of the CSP's infrastructure, including the cloud's topology, policies, data storage methods, and the types of devices that can access the cloud services.

- Cloud customers:
	-  Data may be stored on computers, mobile devices, web browser cache, etc.

- Locally stored cloud data:
	- Popular cloud storage services have sync clients that leave artifacts even when uninstalled.
	- May include info about files that were never synced.


Remember this tool flow:
Can use SQLite browser/Autopsy to check dropbox client's database for artifacts.

Can use google's log analyzer to analyze logs from google drive to look for files. 

There are 2 paths where each of them stores stuff
Dropbox: `C:\Users\\AppData\Local\Dropbox\instance1`
Google Drive: `%USERPROFILE%\AppData\Local\Google\DriveFS`

On cyberchef: Unix timestamp (Miliseconds) to convert the timestamps to real time
md5 hash to compare hashes of the files.


