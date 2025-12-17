# Web Server Notes (EC2 + Linux) – DevOps Reference

---

## 1. What is a Web Server?

A web server is software that serves content over **HTTP/HTTPS**.

Common web servers:

* Apache (httpd)
* Nginx
* Microsoft IIS

Used to serve:

* Static content (HTML, CSS, JS, images)
* Dynamic content (via backend apps)

---

## 2. Static Website Hosting on EC2 (Concept)

Flow:

```
User Browser
   ↓ (HTTP/HTTPS)
DNS / Public IP
   ↓
EC2 Instance
   ↓
Web Server (Apache/Nginx)
   ↓
Document Root (/var/www/html or /usr/share/nginx/html)
```

Key requirements:

* EC2 with public access
* Web server installed and running
* Ports allowed in Security Group

---

## 3. Default Document Roots

| Web Server | Path                  |
| ---------- | --------------------- |
| Apache     | /var/www/html         |
| Nginx      | /usr/share/nginx/html |

`index.html` is served automatically.

---

## 4. Ports and Their Meaning

| Port | Purpose              |
| ---- | -------------------- |
| 80   | HTTP                 |
| 443  | HTTPS                |
| 8080 | Custom / App servers |
| 8443 | HTTPS alternative    |

Web servers **listen** on ports using OS networking.

---

## 5. How to Identify Which Ports Are Open (Server Side)

### List listening ports

```bash
sudo ss -tulpn
```

Filter common web ports:

```bash
sudo ss -tulpn | grep -E '80|443|8080|8443'
```

---

## 6. How to Identify Web Server Type (From URL)

### Using curl

```bash
curl -I https://example.com
```

Look for:

```text
Server: nginx
```

Common values:

* nginx
* Apache
* Microsoft-IIS
* awselb/2.0

⚠️ Headers may be hidden for security.

---

## 7. Identify URL → Server Mapping

### DNS Resolution

```bash
dig example.com
nslookup example.com
```

Maps domain → IP address.

---

## 8. Identify Domains Hosted on a Server (Server Access)

### Apache

```bash
grep -R "ServerName" /etc/httpd/
grep -R "ServerAlias" /etc/httpd/
```

### Nginx

```bash
grep -R "server_name" /etc/nginx/
```

These define **which URLs the server responds to**.

---

## 9. Identify Ports from Web Server Config

### Apache

```bash
grep -R "^Listen" /etc/httpd/
```

### Nginx

```bash
grep -R "listen" /etc/nginx/
```

---

## 10. External Port Testing (No Server Access)

### curl

```bash
curl -I http://example.com
curl -I https://example.com
```

### nc / telnet

```bash
nc -zv example.com 80
nc -zv example.com 443
```

### nmap

```bash
nmap -p 80,443,8080 example.com
```

---

## 11. AWS-Specific Checks (Critical)

Even if the server listens, AWS may block traffic.

### Security Group

* Must allow inbound traffic on required ports

### Network ACL

* Must allow inbound + outbound traffic

---

## 12. Multi-Site Hosting Concept

One server can host **multiple websites** using:

* Apache Virtual Hosts
* Nginx Server Blocks

Same IP, different domains handled via `Host` header.

---

## 13. Common Troubleshooting Checklist

| Layer      | Check                    |
| ---------- | ------------------------ |
| DNS        | Correct IP resolution    |
| EC2        | Instance running         |
| Web server | Service running          |
| Ports      | Listening via ss/netstat |
| Firewall   | iptables/firewalld       |
| AWS SG     | Inbound allowed          |
| NACL       | Not blocking             |

---

## 14. Real-World Examples (curl, nslookup, dig)

### Example 1: Identify Web Server and CDN using curl

```bash
curl -I https://www.amway.com.vn/vn/
```

Sample output (important headers):

```text
HTTP/2 200
server: nginx
via: 1.1 xxxx.cloudfront.net (CloudFront)
x-cache: Miss from cloudfront
x-amz-cf-pop: HYD57-P2
```

What we learn:

* Website uses **Nginx** as web server
* Traffic flows through **AWS CloudFront (CDN)**
* Edge location served from **Hyderabad (HYD)**

---

### Example 2: Identify CDN via nslookup

```bash
nslookup www.amwaylive.com
```

Sample output:

```text
www.amwaylive.com canonical name = d379fiebmtz307.cloudfront.net
Address: 13.32.251.80
Address: 13.32.251.128
```

What we learn:

* Domain uses a **CNAME to CloudFront**
* Multiple IPs indicate **global edge locations**

---

### Example 3: Detailed DNS resolution using dig

```bash
dig www.amwaylive.com
```

Important section:

```text
www.amwaylive.com. IN CNAME d379fiebmtz307.cloudfront.net.
d379fiebmtz307.cloudfront.net. IN A 13.32.251.128
```

What we learn:

* Origin server IP is **hidden**
* Low TTL allows CloudFront to change IPs quickly
* Confirms **enterprise-grade DNS + CDN architecture**

---

## 15. Interview-Ready Summary

> DNS resolution shows the domain pointing to a CloudFront distribution. HTTP headers confirm Nginx as the backend web server. Ports, DNS, CDN, and security headers together define how the website is securely and efficiently delivered.

---

✔ Suitable for GitHub README or DevOps notes repo
✔ Includes real command outputs
✔ Useful for troubleshooting and interviews
