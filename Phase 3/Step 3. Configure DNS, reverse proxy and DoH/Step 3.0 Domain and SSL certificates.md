# Step 3.0 Domain and SSL Certificates

## Introduction
This step focuses on securing your network with a domain name and an SSL certificate. A domain is essential for accessing your services remotely, and using a free or self-signed certificate can lead to security risks and manual maintenance. We'll discuss the importance of separating domain hosting from email and website hosting.

### Key Points:
1. **Domain Requirement**: Ensure you have a real domain name even if it's free for convenience and auto-update capabilities of SSL certificates.
2. **Service Separation**: Understand that domains, emails, and websites are separate services unless bundled together.
3. **SSL Certificates**: Use wildcard certificates to manage subdomains easily. There are three main methods to integrate SSL into your service: direct file upload, manual text entry, or DNS challenge using an API key.

### Example Services:
- **Free Domain Registrars**: Google Domains, Freenom
- **Paid Domain Registrars**: GoDaddy, Namecheap (includes email hosting options)

## SSL Certificates:
Generating a free or paid SSL certificate varies based on your domain registrar. Focus on wildcards for ease of use and managing subdomains. The three main ways to integrate SSL are through file uploads, manual text entry, and DNS challenge with API keys.

### Wildcard Example:
- **Zone Setup**: For `adguard.mydomain.com`, the zone is `adguard.mydomain.com`. Adding a wildcard like `*.mydomain.com` allows all subdomains to be covered without extra configuration.

## Next Steps:
1. **Set up DNS lookups in Adguard**: Configure DNS lookups in Adguard to point to our NPM service on host ip
2. **NPM Configuration**: On NPM, insert our SSL certificate, create an access group, and add proxies for both Adguard and NPM.
3. **Enable DoH in Adguard**: Insert the certificate into Adguard and set it up on client devices for encrypted traffic.
4. **Finalize deployment**: Test the setup, ensuring all traffic is encrypted and adjusting port mappings to secure only port 443.
