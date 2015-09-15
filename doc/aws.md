AWS hack
=======

# Maintenance mode for a site

- The maintenance pages can be hosted on S3, the permission should be set for everyone `list` and `view`, otherwise it will return `403` code

- With CloudFront directing to the null `index.html` page, it creates a `404` no found code. Customize such `404` to `503` code inside CloudFront

- Cross direct the Route53 Recordset (CNAME) and CloudFront (CNAME, alternative) each other for routing.

Finally, either use route53 failover (with weighted routing rule, add extreme weight on maintenance recordset) or just simply redirect the main DNS to maintenance domain.  