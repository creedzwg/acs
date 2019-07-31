# Custom routing - supports multiple HTTPS certificates {#concept_vfv_1yz_xdb .concept}

Use the acs/proxy image [EN-US\_TP\_7111.md\#section\_it1\_v2z\_xdb](reseller.en-US/User Guide/Service discovery and load balancing/Custom routing - User guide.md#section_it1_v2z_xdb) in this example.

**Note:** Services cannot use the same Server Load Balancer; otherwise, the backend machine of the Server Load Balancer will be deleted, and the service will be unavailable.

``` {#codeblock_gqv_2og_kko}
lb:
    image: registry.aliyuncs.com/acs/proxy:0.6
    ports:
            - '80:80'
            - '443:443' # HTTPS must expose this port
    restart: always
    labels:
        # Addon allows the proxy image to function as a subscription registry center and dynamically load the service route.
        aliyun.custom_addon: "proxy"
        # A proxy image container is deployed on each virtual machine (VM).
        aliyun.global: "true"
        # A Server Load Balancer instance is bound to the frontend.
        aliyun.lb.port_80: tcp://proxy_test:80
        aliyun.lb.port_443:tcp://proxy_test:443
    environment:
        # Indicates the range of backend containers that support route loading. "*" indicates the whole cluster. By default, it indicates the services in applications.
        ADDITIONAL_SERVICES: "*"
appone:
    expose:  # For proxied services, use expose or ports to tell proxy containers which port is to be exposed.
        - 80/tcp
    image: 'nginx:latest'
    labels:
        # You can specify paths when configuring URLs. In this example, http/https/ws/wss are supported.
        aliyun.proxy.VIRTUAL_HOST: "https://appone.example.com"
        # Configure the appone certificate.
        aliyun.proxy.SSL_CERT: "-----BEGIN RSA PRIVATE KEY-----\nMIIEpQIBAAKCAQEAvgnKhephWHKWYDEiBiSjzst7nRP0DJxZ5cIOxyXmncd2kslr\nkUIB5qT/MSiJGBL3Lr4advs6kI/JFmxloFrPtwEe2FGkLBfCDXXDrWgxyFhbuPQY\nBLNueUu94sffIxg+4u5Mriui7ftindOAf0d21PSM9gb/ZUypxIgAd3RHCe/gtT0h\nVCn6FikXynXLDTODYWCthQHBwSZS88HNU+B0T9Yl65JiQ0mV+YF+h3D/c232E6Gp\nzK+8ehVB13s5hecUx3dvdUQPBUhJYvzsPjChgsXSMDRexiN66kbhH6dJArsrYb8t\nEBWXfCZaTcF82wkAsUe/fhlGhh97h+66lh6OQQIDAQABAoIBAQC4d8ifNWRI9vIB\nbbAZRne7xMm5MCU2GI8q97Rgm+nAPl5bHinMVsaBnKgaj76EH+TQ+re1xyiSKwCH\nQ7FidsQqYGwQjy9NncJATpAjQ4EPeLWQU2D9Ly+NjnhEKr/u0Ro6LhdA+hqt59dS\nXHvfEP/It5odN62yJzikDWBmk/hhK0tu28dPYUuPoWswXWFMkaNttmfLgZlagiqr\nYp7rxAFqQurzctQ2VNwezekDHQoh8ounHGEniZ+fA6sFtYi83KTKWkvFom1chZQr\nxxPbbgANJJJlNgtkl6JZNxj6SYimmWvzmrrU25khKg/klP5EtQzIx6UFhURnuTKu\nzNgqcIABAoGBAOqUOerveEUePvsAlta8CV/p2KKwenv+kUofQ4UpKFXfnHbQHQfr\nZHS29OQiPxqjVXYLu8gNfLRfKtUNyqV+TDrzJ1elW2RKc00GHAwPbXxijPhmJ2fW\neskn8tlDcyXpvoqWJG34896vo4IbcL0H/eUs0jJo6OJlCQBKXik+t3gxAoGBAM9k\nVOTV2caKyrZ4ta0Q1LKqKfOkt0j+vKz167J5pSLjVKQSUxGMyLnGwiQdDtB4iy6L\nFcCB/S0HM0UWkJWhNYAL8kHry53bVdHtQG0tuYFYvBJo7A+Nppsn9MtlVh8KbVu4\nhOz/3MWwbQNnvIVCGK/fSltS1GhTk4rKL7PjNwMRAoGBALK0n3bqXj6Rrzs7FK6c\na6vlE4PFXFpv8jF8pcyhMThSdPlSzHsHCe2cn+3YZSie+/FFORZLqBAlXBUZP6Na\nFyrlqLgtofVCfppUKDPL4QXccjaeZDDIBZyPUYPQzb05WE5t2WzqNqcUOUVaMEXh\n+7uGrM94espWXEgbX6aeP9lRAoGARlJQ7t8MXuQE5GZ9w9cnKAXG/9RkSZ4Gv+cL\nKpNQyUmoE5IbFKJWFZgtkC1CLrIRD5EdqQ7ql/APFGgYUoQ9LdPfKzcW7cnHic0W\nwW51rkQ2UU++a2+uhIHB4Y3U6+WPO0CP4gTICUhPTo5IQC8vS8M85UZqu41LRA5W\nqnpq1uECgYEAq+6KpHhlR+5h3Y/m0n84yJ0YuCmrl7HFRzBMdOcaW3oaYL83rAaq\n6dJqpAVgeu3HP8AtiGVZRe78J+n4d2JGYSqgtP2lFFTdF9HfhcR2P9bUBNYtWols\nEs3iw53t8a4BndLGBwLPA3lklf7J5stYanRv6NqaRaLq4FQMxsW1A0Q=\n-----END RSA PRIVATE KEY-----\n-----BEGIN CERTIFICATE-----\nMIIDvDCCAqSgAwIBAgIBATANBgkqhkiG9w0BAQUFADBgMQswCQYDVQQGEwJDTjER\nMA8GA1UECBMIWmhlamlhbmcxETAPBgNVBAcTCEhhbmd6aG91MRQwEgYDVQQKEwth\nbGliYWJhLmNvbTEVMBMGA1UECxMMd3d3LnJvb3QuY29tMB4XDTE1MDIwOTA1MzQx\nOFoXDTE2MDIwOTA1MzQxOFowZjELMAkGA1UEBhMCQ04xETAPBgNVBAgTCFpoZWpp\nYW5nMRQwEgYDVQQKEwthbGliYWJhLmNvbTEVMBMGA1UECxMMd3d3LnJvb3QuY29t\nMRcwFQYDVQQDEw53d3cubGluaHVhLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEP\nADCCAQoCggEBAL4JyoXqYVhylmAxIgYko87Le50T9AycWeXCDscl5p3HdpLJa5FC\nAeak/zEoiRgS9y6+Gnb7OpCPyRZsZaBaz7cBHthRpCwXwg11w61oMchYW7j0GASz\nbnlLveLH3yMYPuLuTK4rou37Yp3TgH9HdtT0jPYG/2VMqcSIAHd0Rwnv4LU9IVQp\n+hYpF8p1yw0zg2FgrYUBwcEmUvPBzVPgdE/WJeuSYkNJlfmBfodw/3Nt9hOhqcyv\nvHoVQdd7OYXnFMd3b3VEDwVISWL87D4woYLF0jA0XsYjeupG4R+nSQK7K2G/LRAV\nl3wmWk3BfNsJALFHv34ZRoYfe4fuupYejkECAwEAAaN7MHkwCQYDVR0TBAIwADAs\nBglghkgBhvhCAQ0EHxYdT3BlblNTTCBHZW5lcmF0ZWQgQ2VydGlmaWNhdGUwHQYD\nVR0OBBYEFM6ESmkDKrqnqMwBawkjeONKrRMQMB8GA1UdIwQYMBaAFFUrhN9ro+Nm\nrZnl4WQzDpgTbCBhMA0GCSqGSIb3DQEBBQUAA4IBAQCQ2D9CRiv8brx3fnr/RZG6\nFYPEdxjY/CyfJrAbij0PdKjzZKk1O67chM1Oxs2JhJ6tMqg2sv50bGx4XmbSPmEe\nYTJjIXMY+jCoJ/Zmk3Xgu4K1y1LvD25PahDVhRrPN8H4WjsYu51pQNshil5E/3iQ\n2JoV0r8QiAsPiiY5+mNCD1fm+QN1tyUabczi/DHafgWJxf2B3M66e3oUdtbzA2pf\nYHR8RVeSFrjaBqudO8ir+uYcRbRkroYmY5Vm+4Yp64oetrPpKUPWSYaAZ0uRtpeL\nB5DpqXz9GEBb5m2Q4dKjs5Hm6vyFUORCzZcO4XexDhcgdLOH5qznmh9oMCk9QvZf\n-----END CERTIFICATE-----\n"
    restart: always
apptwo:
    expose:  # For proxied services, use expose or ports to tell proxy containers which port is to be exposed.
        - 80/tcp
    image: 'registry.cn-hangzhou.aliyuncs.com/linhuatest/hello-world:latest'
    labels:
        # You can specify paths when configuring URLs. In this example, http/https/ws/wss are supported.
        aliyun.proxy.VIRTUAL_HOST: "https://apptwo.example.com"
        # Configure the apptwo certificate.
        aliyun.proxy.SSL_CERT: "-----BEGIN RSA PRIVATE KEY-----\nMIIEpQIBAAKCAQEAvgnKhephWHKWYDEiBiSjzst7nRP0DJxZ5cIOxyXmncd2kslr\nkUIB5qT/MSiJGBL3Lr4advs6kI/JFmxloFrPtwEe2FGkLBfCDXXDrWgxyFhbuPQY\nBLNueUu94sffIxg+4u5Mriui7ftindOAf0d21PSM9gb/ZUypxIgAd3RHCe/gtT0h\nVCn6FikXynXLDTODYWCthQHBwSZS88HNU+B0T9Yl65JiQ0mV+YF+h3D/c232E6Gp\nzK+8ehVB13s5hecUx3dvdUQPBUhJYvzsPjChgsXSMDRexiN66kbhH6dJArsrYb8t\nEBWXfCZaTcF82wkAsUe/fhlGhh97h+66lh6OQQIDAQABAoIBAQC4d8ifNWRI9vIB\nbbAZRne7xMm5MCU2GI8q97Rgm+nAPl5bHinMVsaBnKgaj76EH+TQ+re1xyiSKwCH\nQ7FidsQqYGwQjy9NncJATpAjQ4EPeLWQU2D9Ly+NjnhEKr/u0Ro6LhdA+hqt59dS\nXHvfEP/It5odN62yJzikDWBmk/hhK0tu28dPYUuPoWswXWFMkaNttmfLgZlagiqr\nYp7rxAFqQurzctQ2VNwezekDHQoh8ounHGEniZ+fA6sFtYi83KTKWkvFom1chZQr\nxxPbbgANJJJlNgtkl6JZNxj6SYimmWvzmrrU25khKg/klP5EtQzIx6UFhURnuTKu\nzNgqcIABAoGBAOqUOerveEUePvsAlta8CV/p2KKwenv+kUofQ4UpKFXfnHbQHQfr\nZHS29OQiPxqjVXYLu8gNfLRfKtUNyqV+TDrzJ1elW2RKc00GHAwPbXxijPhmJ2fW\neskn8tlDcyXpvoqWJG34896vo4IbcL0H/eUs0jJo6OJlCQBKXik+t3gxAoGBAM9k\nVOTV2caKyrZ4ta0Q1LKqKfOkt0j+vKz167J5pSLjVKQSUxGMyLnGwiQdDtB4iy6L\nFcCB/S0HM0UWkJWhNYAL8kHry53bVdHtQG0tuYFYvBJo7A+Nppsn9MtlVh8KbVu4\nhOz/3MWwbQNnvIVCGK/fSltS1GhTk4rKL7PjNwMRAoGBALK0n3bqXj6Rrzs7FK6c\na6vlE4PFXFpv8jF8pcyhMThSdPlSzHsHCe2cn+3YZSie+/FFORZLqBAlXBUZP6Na\nFyrlqLgtofVCfppUKDPL4QXccjaeZDDIBZyPUYPQzb05WE5t2WzqNqcUOUVaMEXh\n+7uGrM94espWXEgbX6aeP9lRAoGARlJQ7t8MXuQE5GZ9w9cnKAXG/9RkSZ4Gv+cL\nKpNQyUmoE5IbFKJWFZgtkC1CLrIRD5EdqQ7ql/APFGgYUoQ9LdPfKzcW7cnHic0W\nwW51rkQ2UU++a2+uhIHB4Y3U6+WPO0CP4gTICUhPTo5IQC8vS8M85UZqu41LRA5W\nqnpq1uECgYEAq+6KpHhlR+5h3Y/m0n84yJ0YuCmrl7HFRzBMdOcaW3oaYL83rAaq\n6dJqpAVgeu3HP8AtiGVZRe78J+n4d2JGYSqgtP2lFFTdF9HfhcR2P9bUBNYtWols\nEs3iw53t8a4BndLGBwLPA3lklf7J5stYanRv6NqaRaLq4FQMxsW1A0Q=\n-----END RSA PRIVATE KEY-----\n-----BEGIN CERTIFICATE-----\nMIIDvDCCAqSgAwIBAgIBATANBgkqhkiG9w0BAQUFADBgMQswCQYDVQQGEwJDTjER\nMA8GA1UECBMIWmhlamlhbmcxETAPBgNVBAcTCEhhbmd6aG91MRQwEgYDVQQKEwth\nbGliYWJhLmNvbTEVMBMGA1UECxMMd3d3LnJvb3QuY29tMB4XDTE1MDIwOTA1MzQx\nOFoXDTE2MDIwOTA1MzQxOFowZjELMAkGA1UEBhMCQ04xETAPBgNVBAgTCFpoZWpp\nYW5nMRQwEgYDVQQKEwthbGliYWJhLmNvbTEVMBMGA1UECxMMd3d3LnJvb3QuY29t\nMRcwFQYDVQQDEw53d3cubGluaHVhLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEP\nADCCAQoCggEBAL4JyoXqYVhylmAxIgYko87Le50T9AycWeXCDscl5p3HdpLJa5FC\nAeak/zEoiRgS9y6+Gnb7OpCPyRZsZaBaz7cBHthRpCwXwg11w61oMchYW7j0GASz\nbnlLveLH3yMYPuLuTK4rou37Yp3TgH9HdtT0jPYG/2VMqcSIAHd0Rwnv4LU9IVQp\n+hYpF8p1yw0zg2FgrYUBwcEmUvPBzVPgdE/WJeuSYkNJlfmBfodw/3Nt9hOhqcyv\nvHoVQdd7OYXnFMd3b3VEDwVISWL87D4woYLF0jA0XsYjeupG4R+nSQK7K2G/LRAV\nl3wmWk3BfNsJALFHv34ZRoYfe4fuupYejkECAwEAAaN7MHkwCQYDVR0TBAIwADAs\nBglghkgBhvhCAQ0EHxYdT3BlblNTTCBHZW5lcmF0ZWQgQ2VydGlmaWNhdGUwHQYD\nVR0OBBYEFM6ESmkDKrqnqMwBawkjeONKrRMQMB8GA1UdIwQYMBaAFFUrhN9ro+Nm\nrZnl4WQzDpgTbCBhMA0GCSqGSIb3DQEBBQUAA4IBAQCQ2D9CRiv8brx3fnr/RZG6\nFYPEdxjY/CyfJrAbij0PdKjzZKk1O67chM1Oxs2JhJ6tMqg2sv50bGx4XmbSPmEe\nYTJjIXMY+jCoJ/Zmk3Xgu4K1y1LvD25PahDVhRrPN8H4WjsYu51pQNshil5E/3iQ\n2JoV0r8QiAsPiiY5+mNCD1fm+QN1tyUabczi/DHafgWJxf2B3M66e3oUdtbzA2pf\nYHR8RVeSFrjaBqudO8ir+uYcRbRkroYmY5Vm+4Yp64oetrPpKUPWSYaAZ0uRtpeL\nB5DpqXz9GEBb5m2Q4dKjs5Hm6vyFUORCzZcO4XexDhcgdLOH5qznmh9oMCk9QvZf\n-----END CERTIFICATE-----\n"
    restart: always
```

Services appone and apptwo use `aliyun.proxy.VIRTUAL_HOST` to specify the domain names. If you must configure the certificate, set the protocol to https. Then, use `aliyun.proxy.SSL_CERT` to specify the certificate content. The method of configuring the certificate content is as follows:

Assume that the key.pemis a private key file, and ca.pem is a public key file. Run the following commands in the bash \(the current directory contains the public key file and private key file\).

``` {#codeblock_vt9_dre_pom}
$ cp key.pem cert.pem
$ cat ca.pem >> cert.pem
$ awk 1 ORS='\\n' cert.pem
```

Finally, enter the output of the `awk` command as the value of label `aliyun.proxy.SSL_CERT`. Use double quotation marks \(“\) for separation. For other information, such as lb label, [EN-US\_TP\_7033.md\#](reseller.en-US/User Guide/Service orchestrations/lb.md#) see the preceding template and the corresponding [EN-US\_TP\_7112.md\#](reseller.en-US/User Guide/Service discovery and load balancing/Custom routing - simple sample.md#).

