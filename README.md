# Unplugged-pics
Pictures for my presentation at osint event.

- Maltego and Nmap
![Maltego and Nmap](https://raw.githubusercontent.com/DeadmanXXXII/Unplugged-pics/main/Screenshot_20241105-142422.png)

- Nikto
![Nikto](https://raw.githubusercontent.com/DeadmanXXXII/Unplugged-pics/main/Screenshot_20241105-142441.png)

- LS5
![LS5](https://raw.githubusercontent.com/DeadmanXXXII/Unplugged-pics/main/Screenshot_20241105-143927.png)

![Ls5](https://raw.githubusercontent.com/DeadmanXXXII/Unplugged-pics/main/Screenshot_20241105-143914.png)

- Exiftool P.H.
![Exiftool](https://raw.githubusercontent.com/DeadmanXXXII/Unplugged-pics/main/Screenshot_20241105-144826.png)

![Exiftool](https://raw.githubusercontent.com/DeadmanXXXII/Unplugged-pics/main/Screenshot_20241105-144819.png)

- Tweepy
![Tweepy](https://raw.githubusercontent.com/DeadmanXXXII/Unplugged-pics/main/Screenshot_20241105-144448.png)

- Telethon
![Telethon](https://raw.githubusercontent.com/DeadmanXXXII/Unplugged-pics/main/Screenshot_20241105-160335.png)

- Shodan
![Shodan](https://raw.githubusercontent.com/DeadmanXXXII/Unplugged-pics/main/Screenshot_20241105-160649.png)

- Bind9
![Bind9](https://raw.githubusercontent.com/DeadmanXXXII/Unplugged-pics/main/Screenshot_20241105-160922.png)

- Geopy
![Geopy](https://raw.githubusercontent.com/DeadmanXXXII/Unplugged-pics/main/Screenshot_20241105-161157.png)

- The Harvester
![Harvester](https://raw.githubusercontent.com/DeadmanXXXII/Unplugged-pics/main/Screenshot_20241105-162009.png)

- Social mapper GreenWolf
![Social mapper](https://raw.githubusercontent.com/DeadmanXXXII/Unplugged-pics/main/Screenshot_20241105-162455.png)

- Creepy
![Creeps](https://raw.githubusercontent.com/DeadmanXXXII/Unplugged-pics/main/Screenshot_20241105-162543.png)

- Google Dorks
![Dorks](https://raw.githubusercontent.com/DeadmanXXXII/Unplugged-pics/main/Screenshot_20241105-162649.png)

- Open CV
![OoenCV](https://raw.githubusercontent.com/DeadmanXXXII/Unplugged-pics/main/Screenshot_20241105-163639.png)


---

1. Shodan Script for Continuous IP Scanning and Geo-Location

This Python script uses Shodan to scan for internet-connected devices within a specific geographic range and retrieve their location. It uses OpenCage Data to convert latitude and longitude to an 8-figure grid reference.

shodan_scan.py
```python
import shodan
import requests

# Initialize Shodan API
SHODAN_API_KEY = 'your_shodan_api_key'
api = shodan.Shodan(SHODAN_API_KEY)

# Search query to find devices with GPS data
query = 'geo:"51.5074,-0.1278" port:80,443 has_screenshot:true'

# Make the query
results = api.search(query)

# Function to get 8-figure grid reference using latitude and longitude
def get_grid_reference(lat, lon):
    url = f'https://api.opencagedata.com/geocode/v1/json?q={lat}+{lon}&key=your_opencagedata_api_key'
    response = requests.get(url).json()
    # Parse response and get grid reference if available
    try:
        grid_ref = response['results'][0]['annotations']['OSM']['grid_reference']
        return grid_ref
    except KeyError:
        return "Grid reference not available"

# Loop through results
for result in results['matches']:
    ip = result['ip_str']
    lat = result['location']['latitude']
    lon = result['location']['longitude']

    # Get grid reference
    grid_ref = get_grid_reference(lat, lon)

    print(f"IP: {ip}, Latitude: {lat}, Longitude: {lon}, Grid Reference: {grid_ref}")
```

---

2. Nmap Script for Continuous Scanning

This bash script uses Nmap to scan for devices within specific IP ranges, checking for open ports and potential vulnerabilities. The script can be run periodically via a cron job.

nmap_scan.sh
```bash
#!/bin/bash

# Define a list of target IP ranges
target_ips=("192.168.1.0/24" "203.0.113.0/24")

# Loop through each range and scan using Nmap
for ip in "${target_ips[@]}"
do
    echo "Scanning $ip"
    nmap -sV --script=vuln $ip -oN scan_results.txt
done
```

---

3. IP Geolocation and Distance Calculation

This Python script retrieves the geolocation (latitude and longitude) of an IP address and calculates the distance from the target. It is useful for finding the proximity of devices to a specific location.

ip_geolocation.py
```python
import requests
from geopy.distance import geodesic

def get_ip_location(ip):
    url = f'http://ipinfo.io/{ip}/geo'
    response = requests.get(url).json()
    if 'loc' in response:
        lat, lon = response['loc'].split(',')
        return float(lat), float(lon)
    return None, None

# Function to calculate distance between two locations
def calculate_distance(lat1, lon1, lat2, lon2):
    location1 = (lat1, lon1)
    location2 = (lat2, lon2)
    return geodesic(location1, location2).km

# Example IP and target location
ip = '8.8.8.8'  # Google DNS as an example
target_lat, target_lon = 51.5074, -0.1278  # London

# Get the location of the IP
ip_lat, ip_lon = get_ip_location(ip)

# Calculate the distance to the target
if ip_lat and ip_lon:
    distance = calculate_distance(target_lat, target_lon, ip_lat, ip_lon)
    print(f"Distance to target: {distance:.2f} km")
```


4. Social Media Checkers

For tracking social media profiles, messaging, and posts related to the identified IPs, we can use tools such as theHarvester, Social Mapper, and API access to platforms like Telegram or Twitter.

A. Social Media Profile Search with theHarvester

theHarvester is a tool that can gather information such as emails, names, and social profiles from various platforms (LinkedIn, Twitter, etc.) related to a domain or IP range.

Command to use theHarvester:

# theHarvester example command for LinkedIn and Twitter
```
theHarvester -d targetdomain.com -b linkedin,twitter
```
You can also automate this using a script:

social_media_harvest.sh
```bash
#!/bin/bash

# Run theHarvester to find social media profiles related to the IP/domain
target="targetdomain.com"
output_file="harvest_results.txt"

# Search for LinkedIn and Twitter profiles
theHarvester -d $target -b linkedin,twitter -f $output_file

echo "Social media profiles saved in $output_file"
```
B. Social Media Correlation with Social Mapper

Social Mapper uses facial recognition to correlate social media profiles across different platforms. It takes an image or list of people and searches for matching profiles on LinkedIn, Facebook, Instagram, etc.

Social Mapper Example Command:
```
# Social Mapper to correlate LinkedIn profiles
python social_mapper.py -f imagefolder -i "linkedin" -m fast
```
You can create a folder with images of individuals and Social Mapper will search for profiles across platforms.


---

C. Telegram Public Channel Monitoring

You can use Telegram's API to monitor public channels and look for recurring messages related to the target.

1. Set up a Telegram Bot to get the API key.


2. Use the Telethon Python library to interact with Telegram.



telegram_monitor.py

```python
from telethon.sync import TelegramClient

# Replace these with your Telegram API credentials
api_id = 'your_api_id'
api_hash = 'your_api_hash'
phone = 'your_phone_number'

# Initialize the Telegram client
client = TelegramClient(phone, api_id, api_hash)
client.start()

# Target public channel or group
target_channel = 'target_public_channel'

# Fetch messages from the target channel
for message in client.iter_messages(target_channel):
    print(f"Sender ID: {message.sender_id}, Message: {message.text}")
```

This script monitors public Telegram channels and tracks messages from specific users.
Also use and automate Telethon.

---

D. Twitter API for Public Tweets Monitoring

To track public tweets related to your targets, use the Twitter API via tweepy.

1. Set up Twitter Developer API credentials.


2. Use Tweepy to fetch and track tweets containing specific keywords or hashtags.



twitter_monitor.py

```python
import tweepy

# Replace with your Twitter API credentials
API_KEY = 'your_api_key'
API_SECRET_KEY = 'your_api_secret_key'
ACCESS_TOKEN = 'your_access_token'
ACCESS_TOKEN_SECRET = 'your_access_token_secret'

# Authenticate to Twitter
auth = tweepy.OAuthHandler(API_KEY, API_SECRET_KEY)
auth.set_access_token(ACCESS_TOKEN, ACCESS_TOKEN_SECRET)
api = tweepy.API(auth)

# Define the keyword/hashtag to track
keyword = 'target_keyword'

# Fetch public tweets containing the keyword
for tweet in tweepy.Cursor(api.search, q=keyword, lang="en").items(50):
    print(f"User: {tweet.user.screen_name}, Tweet: {tweet.text}")
```

This script retrieves public tweets containing a specific keyword or hashtag related to your target.


---

---

5. Creepy: Geolocation Tracking

Creepy is a tool that gathers location-related information from social networking platforms and image metadata (such as GPS coordinates).

Step 1: Install Creepy

To install Creepy:

```
sudo apt-get install creepy
```

Step 2: Use Creepy for Geolocation

Creepy will search public social media accounts for posts that contain geolocation data (like check-ins or geotagged images). It can also extract metadata from images uploaded online.


1. Launch Creepy.


2. Select the Target Username (for example, a social media username).


3. Specify the Platforms (Twitter, Instagram, Flickr, etc.).


4. Run the query to gather geolocation data and map it on a world map.



You can export the data to CSV or KML for further analysis.

Automating Creepy with a Python Wrapper:

Creepy doesn’t have an official Python API, but you can automate its functionality by parsing the output files (e.g., CSV). Here’s an example of how you could wrap Creepy in Python:

```python
import os

# Define the target username and output path
username = "target_username"
output_file = f"/path/to/output/{username}_creepy_output.csv"

# Run Creepy from command line (adjust path to creepy as needed)
os.system(f"creepy --user {username} --output {output_file}")

# Parse and analyze the output CSV
import pandas as pd
data = pd.read_csv(output_file)

# Process the geolocation data (for example, plotting it on a map)
for index, row in data.iterrows():
    print(f"Location: {row['latitude']}, {row['longitude']}, Date: {row['timestamp']}")
```

---

6. Nmap: Network Mapping and Vulnerability Scanning

Nmap can be used for network reconnaissance and vulnerability scanning, identifying open ports and services across devices.

Step 1: Advanced Nmap Scan with Scripts

The following Nmap command scans for devices with specific services (e.g., HTTP, SSH) and checks for known vulnerabilities:

```nmap
nmap -sV --script=vuln --script-args=unsafe=1 <target_ip_range> -oN nmap_scan_results.txt
```

-sV: Service detection to identify running services.

--script=vuln: Run vulnerability scripts to detect known vulnerabilities.

--script-args=unsafe=1: Enables potentially dangerous script scanning.

-oN nmap_scan_results.txt: Save the scan results to a file.


Automate Nmap with Python:

Here’s how you can integrate Nmap with your Python-based OSINT platform:

```python
import os

# Define target IP range and output file
target_ip_range = "192.168.1.0/24"
output_file = "nmap_scan_results.txt"

# Run Nmap scan
os.system(f"nmap -sV --script=vuln --script-args=unsafe=1 {target_ip_range} -oN {output_file}")

# Parse and analyze Nmap output
with open(output_file, 'r') as file:
    scan_results = file.readlines()

# Print or process the results
for line in scan_results:
    if "VULNERABLE" in line:
        print(line)
```

---

7. Maltego: Advanced Network and Relationship Mapping

Maltego is a powerful data mining and visualization tool that allows you to gather OSINT data from various sources and map relationships between people, organizations, and online entities.

Step 1: Install and Launch Maltego

Install Maltego from the official website: Maltego Downloads.

Step 2: Use Maltego Transforms

Maltego allows you to run transforms (automated data-mining queries) that collect data from:

Social media profiles

Domain names

Email addresses

IP addresses


Example of mapping a target email to associated entities:

1. Start with the email address of the target.


2. Run the Email to Social Media transform to discover linked social media profiles.


3. Run the Domain to DNS Names transform to map domains to IPs and DNS servers.


4. Use the Person to Address transform to find physical locations.



Automating Maltego Using Paterva Transforms in Python:

Here’s how you can call Maltego’s Python API for automating certain transforms:

```python
from maltego_trx.transform import DiscoverableTransform
from maltego_trx.maltego import UIM_TYPES

class SocialMediaTransform(DiscoverableTransform):
    @classmethod
    def create_entities(cls, request, response):
        target_email = request.Value
        # Example transform logic to find social media profiles linked to an email
        social_profiles = find_social_profiles(target_email)  # Custom logic
        for profile in social_profiles:
            response.addEntity('maltego.SocialMedia', profile)
```
This will allow you to build custom transforms and include them in your automated OSINT tool.


---

8. Metadata Extraction with ExifTool

ExifTool extracts metadata from images and other media files, which often includes GPS data, camera settings, timestamps, and more.

Step 1: Install ExifTool
```
sudo apt-get install exiftool
```
Step 2: Extract Metadata from Images

Run the following command to extract all metadata from an image:
```
exiftool image.jpg
```
To automate metadata extraction and integrate it into your OSINT platform, here’s a Python script that calls ExifTool and processes the output:

```python
import subprocess
import json

# Function to extract metadata using ExifTool
def extract_metadata(file_path):
    result = subprocess.run(['exiftool', '-json', file_path], stdout=subprocess.PIPE)
    metadata = json.loads(result.stdout.decode('utf-8'))
    return metadata

# Test the function
metadata = extract_metadata('image.jpg')
print(metadata)
```

This script can be extended to process multiple files, automatically storing GPS data, timestamps, and other metadata into a database.


---




