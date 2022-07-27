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
    street: Amstelveenseweg 728B
    city: Amsterdam
    region: Noord-Holland
    postcode: 1081jK
    country: The Netherlands
    country_code: NL
  coordinates:
    latitude: '37.4275'
    longitude: '-122.1697'
  directions: Enter Building 1 and take the stairs to Office 200 on Floor 2
  office_hours:
    - 'Monday 10:00 to 13:00'
    - 'Wednesday 09:00 to 10:00'
  appointment_url: 'https://calendly.com'
  contact_links:
    - icon: linkedin
      icon_pack: fab
      name: Connect with me
      link: 'https://linkedin.com/in/andresguzco'

design:
  columns: '2'
---
