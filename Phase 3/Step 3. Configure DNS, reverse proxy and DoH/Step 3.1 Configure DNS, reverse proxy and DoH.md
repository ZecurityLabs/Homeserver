# Step 3.1 Configure DNS, Reverse Proxy, and DoH

## Introduction
On the following steps, there will be some back-and-forth between our AdGuard and NPM admin panels, but the order must be retained otherwise we risk losing access to the services before finishing the configuration. It is highly recommended to open both services in separate browser tabs for ease of navigation.

## Step 1: Configure DNS Rewrites in AdGuard
Assuming we have started and set up our AdGuard container with initial setup, we can now access the admin dashboard at `http://192.168.0.5:80`. We should see that AdGuard is working as expected, displaying metrics and blocked DNS requests (assuming a client is configured to use `192.168.0.5`).
Let's navigate to the "Filters" tab and go to "DNS rewrites". Add two entries for our domain and wildcard:
- `mydomain.com - 192.168.0.5`
- `*.mydomain.com - 192.168.0.5`

**Hint:** If you encounter issues saving the entry, check for spaces at the start or end of the IP address.

## Step 2: Configure Reverse Proxy in NPM
Next, we will access the NPM admin dashboard at `http://192.168.0.5:81`. We need to perform three tasks:
1. **Insert SSL Certificate:** Add our domain's SSL certificate for `mydomain.com` and `*.mydomain.com`. Use DNS challenge on Let's Encrypt, generate a DNS API token if necessary, and paste it into the API text field.
2. **Restrict Access:** Create an access group named "LocalUsers" with local IP range (e.g., `192.168.0.0/24`). Save this access list.
3. **Add Proxies:** In NPM, navigate to "Hosts / Proxy Hosts". Configure proxies for AdGuard and NPM:
   
**NPM Proxy**
- Domain Name: `npm.mydomain.com`
- Scheme: `http`, Hostname: `npm`, Port: 81
- Access List: LocalUsers
- Enable all toggles except WebSocket. Move to the SSL tab, select the SSL certificate, and enable all toggles.

**AdGuard Proxy**
- Domain Name: `adguard.mydomain.com`
- Scheme: `http`, Hostname: `adguard`, Port: 81 (We will modify this later to use HTTPS)
- Access List: LocalUsers
- Enable all toggles except WebSocket. Move to the SSL tab, select the SSL certificate, and enable all toggles.

**Note:** When using Chrome for accessing services, subdomains like `dns.mydomain.com` or `adguard.mydomain.com` may trigger a warning due to security settings. We can use workarounds by renaming URLs temporarily. But for simplicity we will use `adguard.mydomain.com` and `npm.mydomain.com`.

## Step 3: Testing the Configuration
Let's access AdGuard at `https://adguard.mydomain.com` and NPM at `https://npm.mydomain.com`. Verify that we can log in without issues. If access is denied, we can check server firewall rules, client DNS configuration, and flush DNS on the client machine.

## Step 4: Enable DoH on AdGuard
Back on Adguard Admin panel navigate to "Settings / Encryption Settings" and enable encryption settings. Configure Server Name as `adguard.mydomain.com`, redirect to HTTPS automatically, and insert the SSL certificate. Save changes, which will temporarily disable access to the admin panel. This is expected behavior.
## Step 5: Update NPM and Client DNS Settings
Update the NPM proxy settings for AdGuard: change scheme to `https` and port to 443. Update client DNS settings to enable DoH and use the URL `https://adguard.mydomain.com/dns-query`.

## Step 6: Further Testing
Ensure all services are accessible over HTTPS, and clients configure DoH properly.

## Step 7: Clean Up
Update Docker compose files to remove all port mappings (except port 443 on NPM). Restart containers, and ensure AdGuard and NPM are not accessible via `http://192.168.0.5:80` or `http://192.168.0.5:81`. If issues persist, clear browser cache. We can also go back to our server firewall and allow only port 443 to secure our server further.

## Step 7: Celebration
We have secured our traffic with encrypted DNS over HTTPS, restricted access to local IPs only, and closed unnecessary ports on the server.

## How It Works
Regarding the query about exposing AdGuard's IP through port 443 after enabling DNS over HTTPS (DoH), it is important to clarify that this does not happen. This was a deliberate security measure implemented in our setup. 

AdGuard, which handles DNS requests internally, points them to NPM's IP. Additionally, AdGuard's traffic itself is now encrypted through DoH. The port 443 on our home server is specifically for NPM, not directly for AdGuard. We have not added any proxy configuration for this port in NPM; instead, we access it via HTTP on port 81 using the `http` scheme from an encrypted URL.

Therefore, if someone were to attempt to access `https://192.168.0.5:443`, they would receive an SSL error indicating that the name was not found because there is no such configuration. Instead, when NPM is asked about `https://adguard.mydomain.com`, knowing AdGuard is on the same network (referred to as "adguard"), it forwards the traffic within the isolated network rather than exposing AdGuard's IP address.

This forwarding might look like `https://127.72.0.3:443` depending on the bridge network IP range, and we can verify these details by inspecting our Docker networks and containers. 

NPM will follow this same forward approach for every proxy host we add in the future, which is why we do not need to expose any additional ports. We don't even have to set static IPs on containers within this bridge network.

## Next Steps
- Optionally deploy more containers and use them securely without mapping ports.

## Phase 3 Project Completion Reflection

Completing Phase 3 of my project was not only rewarding but also highly educational, particularly given the troubleshooting insights documented. Ensuring my server's access is secure marked a significant milestone in my journey. Moving forward to Phase 4, I will incorporate robust security practices into how I deploy and manage our containers. I will delve into editing Docker files and images to ensure they are free from vulnerabilities and conform to compliance standards.

This phase is crucial as it extends beyond mere network security—it involves the integrity of my entire infrastructure. If a service is exposed to the public internet, the safety of the container becomes paramount. Unsecured containers could lead to breaches originating from within the system, potentially spreading outward.

In addition to enhancing container security, I will introduce a compliance monitoring service for my server (a headless Debian 12 setup). This service will continuously scan my server's configuration for vulnerabilities, missing security updates, and ensure adherence to enterprise-level policies such as GDPR. Understanding these practices not only fortifies our current infrastructure but also prepares us for more complex deployments in the future.

By mastering Docker deployment and security, we are well-positioned to extend these skills to Kubernetes and cloud environments, thereby enhancing both our technical expertise and operational robustness.
