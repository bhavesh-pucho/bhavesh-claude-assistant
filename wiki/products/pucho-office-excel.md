# Pucho Office — Excel Add-in

**Summary**: Office.js Excel add-in with AI tool-calling, published on AppSource, served via Nginx.
**Tags**: #pucho-office #excel #office-js #add-in
**Created**: 2026-04-07
**Last Updated**: 2026-04-07

---

## Overview

- **Stack**: Office.js add-in
- **URL**: excel.office.pucho.ai
- **Distribution**: Microsoft AppSource
- **Serving**: Nginx static hosting on EC2

## API Integration

Uses [[pucho-api-schema]] streaming endpoint with flat tool-call format.

## Deployment Notes

- Served via Nginx (not Traefik) for static files
- `client_max_body_size 50M` required in Nginx config
- No localStorage/sessionStorage — not supported in Office add-in context

## Related Notes

- [[pucho-api-schema]]
- [[pucho-office-ppt]]
- [[ec2-setup]]
