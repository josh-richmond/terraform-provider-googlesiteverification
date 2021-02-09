# Terraform provider for Google's site verification

A simple provider hitting this API: https://developers.google.com/site-verification

https://registry.terraform.io/providers/hectorj/googlesiteverification

## How to install it?

### Terraform v0.13+

See https://registry.terraform.io/providers/hectorj/googlesiteverification -> "Use provider"

## Older terraform

Download a binary from https://github.com/hectorj/terraform-provider-googlesiteverification/releases, then either:

- from anywhere, run `./terraform-provider-googlesiteverification install home`
- from your project's terraform dir, run `./terraform-provider-googlesiteverification install`

If you prefer doing it manually, see https://www.terraform.io/docs/extend/how-terraform-works.html#discovery

## Usage

It requires Google credentials to be provided the same way as described as in this document: https://registry.terraform.io/providers/hashicorp/google/latest/docs/guides/provider_reference#authentication

```hcl
# We get the verification token from Google.
data "googlesiteverification_dns_token" "example" {
  domain = "yourdomain.example.com"
}

# We put it in our DNS records.
# Here is an example with Cloudflare, but you should be able to adapt it to any DNS provider.
resource "cloudflare_record" "verification" {
  zone_id = "{your zone ID}"
  name    = data.googlesiteverification_dns_token.example.record_name
  value   = data.googlesiteverification_dns_token.example.record_value
  type    = data.googlesiteverification_dns_token.example.record_type # "TXT" only for now, but it's better to use the data value for future-proofing.
}

# *After* that, we submit our verification request to Google.
# Might take some time, depending on Google's DNS caching.
resource "googlesiteverification_dns" "example" {
  domain     = "yourdomain.example.com"
  token      = data.googlesiteverification_dns_token.example.record_value
}
```

To import the resource:

```bash
terraform import "googlesiteverification_dns.example" "yourdomain.example.com"
```
