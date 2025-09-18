# Domain Setup and Cloudflare Integration

## 1. Buy a Domain
- Domain used in this project: **mtritran.click**
- Purchased from a registrar (e.g., Tenten).

## 2. Add Domain to Cloudflare
1. Create an account on [Cloudflare](https://www.cloudflare.com/).
2. Add the domain `mtritran.click` to Cloudflare.
3. Cloudflare will scan for existing DNS records and import them.
4. At your registrar (Tenten), update the nameservers to the ones provided by Cloudflare.

Example:
ns1.cloudflare.com
ns2.cloudflare.com

## 3. Example Architecture
User → Cloudflare DNS → Public IP (Load Balancer) → Teleport VM (via Private IP)