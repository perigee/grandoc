AWS hack
=======

# Maintenance mode for a site

- The maintenance pages can be hosted on S3, the permission should be set for everyone `list` and `view`, otherwise it will return `403` code

- With CloudFront directing to the null `index.html` page, it creates a `404` no found code. Customize such `404` to `503` code inside CloudFront

- Cross direct the Route53 Recordset (CNAME) and CloudFront (CNAME, alternative) each other for routing.

Finally, either use route53 failover (with weighted routing rule, add extreme weight on maintenance recordset) or just simply redirect the main DNS to maintenance domain. Following figure demonstrates the routing setting for Route53, Cloudfront and S3. 


![Routing](images/maintenancemode.jpg "Routing setting")




# Route53 DNS failover

## ChangeBatch dict in `boto3`


```python
changeBatch = {
        'Comment': 'Creado desde mi aplicaciden',
        'Changes': [
            {
                'Action': 'CREATE',
                'ResourceRecordSet': {
                    'Name': 'xpg.testing.com.',
                    'SetIdentifier': 'xpg-Primary',
                    'Type': 'A',
                    'TTL': 30,
                    'ResourceRecords': [
                        {
                            'Value': '52.3.53.217'
                        },
                    ],
                    'Failover': 'PRIMARY',
                    'HealthCheckId': 'ff308f9a-2ff1-4627-9d18-bfb8d2f1fe18',
                }
            },
        ]
    }
```