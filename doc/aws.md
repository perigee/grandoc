AWS hack
=======

# Maintenance mode for a site

- The maintenance pages can be hosted on S3, the permission should be set for everyone `list` and `view`, otherwise it will return `403` code

- With CloudFront directing to the null `index.html` page, it creates a `404` no found code. Customize such `404` to `503` code inside CloudFront

- Cross direct the Route53 Recordset (CNAME) and CloudFront (CNAME, alternative) each other for routing.

Finally, either use route53 failover (with weighted routing rule, add extreme weight on maintenance recordset) or just simply redirect the main DNS to maintenance domain. Following figure demonstrates the routing setting for Route53, Cloudfront and S3. 


![Routing](images/maintenancemode.jpg "Routing setting")



## Attention

- The multiple redirection of alias domain may be broken because of the CNAME setting in CloudFront, so you can use wildcard inside CloudFront's `alternate domain name` to accept all the subdomain. 
- Another solution may just launch up an instance with maintenance page only, and join it into ELB. 



# Route53 DNS failover

## ChangeBatch dict example 

### create the failover primary in `boto3`


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

### update a simple recordset with type A in `boto3`

```python 
changBatch = {
        'Comment': 'update record set',
        'Changes': [
            {
                'Action': 'UPSERT',
                'ResourceRecordSet': {
                    'Name': 'stagedb.inhouse.ly.',
                    'Type': 'A',
                    'TTL': 30,
                    'ResourceRecords': [
                        {
                            'Value': '10.0.2.248'
                        },
                    ],
                }
            },
        ]
    }

response = client.change_resource_record_sets(
    HostedZoneId='hostidiididiid',
    ChangeBatch=changeBatch)
```


## ELB

- For the moment, the ELB only supports instances. 
- Be careful with the visitors IP through ELB, it may appear only ELB ip in log.



# References

The tags are put into *[ ]* for easy recalling.

- [cloudfront, s3, route53] http://www.michaelgallego.fr/blog/2013/08/27/static-website-on-s3-cloudfront-and-route-53-the-right-way/
- [cloudfront, s3, ssl] https://bryce.fisher-fleig.org/blog/setting-up-ssl-on-aws-cloudfront-and-s3/
- [cloudfront, s3, ssl] https://www.tiredpixel.com/2014/09/01/secure-static-websites-using-aws-s3-ssl-aws-cloudfront-and-aws-route-53/
- [blog, aws, python] https://www.tiredpixel.com/
