- Investigation of criminal activity that has occurred on the web services over the internet. (history, cache, server logs)

- Discoverable Evidences
	- Downloaded images, videos, documents, exe files/scripts
	- Data entered into forms, queries, logins and passwords and financial info. 
	- Bookmarks, favorites, searches


## Browsers
Top 3 browsers by market share: Chrome, Safari, Edge

## Private Browsing
- Standard mode: History writes, cache, cookies on disk (Sqlite)
- Private mode: Writes mainly on RAM. When window closed, memory deallocated and data dissappears

Forensic artifacts:
- RAM analysis: Capture RAM dump, search for strings (URLs, keywords) or reconstruct browser objects.
- Network analysis: DNS cache, router/isp logs
- File system artifacts (Pagefile.sys / Hyberfil.sys)
- Cloud platform analysis.


- Tools: AccessData FTK, Magnet Axiom, Nirsoft bulk_extractor


## Difficulties
- Many browsers, volume of data, different formats
	- history, cookies, cache files

- Encryption and security features 

## Web forensic artifacts:
- History 
- cache
- cookies
- typed urls
- sessions
- most visited sites
- screenshots
- financial info
- form values
- downloaded files
- favorites

## QUdpInternetConnections
- UDP based secure transport layer protocol developed by google in 2012.
- used by Chromium based browser to communicate with google's servers. 
- Combines the handshake and data transmission phases. (Less latency)

![[Pasted image 20260505021124.png]]

## Cookies
- Keys values stored locally on the client.
- Name, value, expiration date and route information. 

## Sessions
![[Pasted image 20260505023159.png]]

## Web cache
- Stores web resources temporarily to reduce server lag. 
- Browser requests something -> checks if cache has it and if it is fresh.
- Cache stores resources until they expire or get updated on the server.
- Components:
	- Cache-control headers
	- Expires headers: Legacy method for defining resource lifetime
	- ETags: Validators to ensure resource freshness. 



## Artifact Locations
`C:\Users\<User>\AppData\ or %USERPROFILE%\AppData\`
Local -> Cache, temporary data
Roaming -> Sync-related data

- Use sqlite databases and use json files

## how is the data stored
- index.dat: IE 9/earlier an explorer
	- Structured "MS IE Cache file format"
	- record of accessed URLs, including search queries, web mail accesses, cache, cookies and etc.

- WebCacheV01.dat: Win 10/8, IE 11/10/8
	- indexing functions are being implemented within a Microsoft database system.
	- + counters.dat, container.dat, suggestedsites.dat
	- C:\Users\username\AppData\Local\Microsoft\Windows\WebCache


- spartan.edb: Microsoft Edge (not in windows 11)




Web browser artifacts:
	- Physical dumps of mobile devices
	- File systems of mobile devices
	- Backups of mobile devices
	- Data, which can be extracted from Clouds.
	- Hard drives.
	- Images of hard drives
	- memory dumps.
	- hibernation and page files



## Fiddler web debugger

![[Pasted image 20260505025915.png]]

## Autopsy
