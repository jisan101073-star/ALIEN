# ALIEN Workspace — Final UI build

This final UI build keeps the earlier v2 sidebar and navigation, with a refined workspace layout.

## Working in this frontend
- Original sidebar navigation: Overview, Projects, Domains, Activity, Settings
- Light/dark theme switch saved on this device
- Create, edit, delete, search and filter projects
- ALIEN subdomain and custom-domain validation (`jisan.com` stays dotted)
- Select and store website source ZIP locally in the browser using IndexedDB; download it later
- Export/import project details and activity as JSON
- Responsive layout and mobile sidebar overlay

## Hosting status
This is still the frontend workspace, not the live hosting engine. Project records and ZIP files are stored in the current browser/device. ZIPs are not uploaded to a server, and websites are not live yet. Real publishing, wildcard subdomains, DNS verification and online file hosting require the Cloudflare Worker + R2 + D1 backend. JSON backups do not contain ZIP file data.

## Preview deploy
Upload `index.html` to a static hosting service to serve this interface. This alone does not enable ALIEN website hosting.
