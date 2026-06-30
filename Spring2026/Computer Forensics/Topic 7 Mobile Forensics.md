- Things change fast with mobile phones. Structures change, constantly moving around, easy to be contaminated and hard to isolate.

- Info stored:
	Personal info: Contact lists, messages, emails, calendar events, notes, reminders.
	
	Multimedia
	App Data: fitness, social, finance
	Location Data: GPS and other location tracking services
	Auth Data: Biometric Data (fingerprints, facial recognition)
	Payment info
	Health and Medical info
	Web browsing info
	Device setting and configs (Wi-fi networks and passwords)

- Modern Examination methods:
	- OEM flash tools (flasher, twister boxes): Overwriting non-volatile memory ROM, copying from the memory, debugging. (samsung kies, SP flash tool, Odin, Emma)
	- Commercial/Automated tools. Belkasoft Evidence center, MPE


## Mobile Networks
- 2g, 2g+, 3g, 4g, 5g, Wi-fi
 - Cellmapper tool
 
## SIM card and IED forensics
- Terrorists use cellphones to detonate IEDs
- Sim card chip contains:
	- Name of operator, Serial number of card, contacts, last dialed numbers, PIN, PUK code
	- SMS messages, deleted, sent, received


![[Pasted image 20260504152021.png]]


## Acquisition Techniques 
- Manual acquisition: Manually interfacing with the device
- File system acquisition : Grab data, paritions, storage.
- Physicial acquisiting: bit by bit copy of the device's flash memory/ disk.


## Methods to bypass mobile device security
- Exploit vuln and unlock the phone (Cellebrite or GrayKey)
- Special modes: Bootloader or recovery mode on androids.
- Software bugs that allow access without passcode.
- Cloud backups: w legal authorization, obtain data from iCloud Google drive
- Companion devices: Extract data from synced devices
- SIM or SD cards: remove SIM or memory cards for stored data. 


## Manual Acquisition in Android


## ADBridge for evidence acquisition
- Why? : Enables data extraction without messign with the internals.
- App data, Logs (logcat), File system artifacts, System Info
- Pros: Easy, flexible
- Cons: Very slow, Compromise data integrity, Can be halted if device locked, cannot recover hidden/deleted information. 


## IOS HFSX/HFS+ hierarchical file system (plus) used in modern iOS devices.

- Plists (property lists) : XML info about apps
- iLEAPP: ios logs, events, plists parser
- Stuff in databases (SQLite)
- Deleted data moved to .Trashes\501 folder until overwritten


## Physical Acquisition: JointTAGgroup
- Avanced acquisition method, involves connecting to test access ports (TAPs) on a device
- Full extraction of physical image from devices.
- Forcing the processor to transfer the raw data stored on connected memory chips.

