---
# An instance of the Contact widget.
widget: contact

# This file represents a page section.
headless: true

# Order that this section appears on the page.
weight: 130

title: Contact
subtitle:

content:
  # Automatically link email and phone or display as text?
  autolink: true

  # Email form provider
  form:
    provider: Google
    formspree:
      id:
    netlify:
      # Enable CAPTCHA challenge to reduce spam?
      captcha: false

  # Contact details (edit or remove options as required)
  email: andres@andresguzco.com
  phone: +31 (0) 6 14 34 4290
  address:
    city: Amsterdam
    region: Noord-Holland
    country: The Netherlands
    country_code: NL
  contact_links:
    - icon: linkedin
      icon_pack: fab
      name: Connect with me
      link: 'https://linkedin.com/in/andresguzco'

design:
  columns: '2'
---
