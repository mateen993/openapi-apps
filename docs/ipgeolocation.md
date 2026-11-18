# IPGeolocation

[IPGeolocation.io](https://ipgeolocation.io) enriches IP addresses with location, ASN, network and company data, IP security signals (threat score, VPN, proxy, residential proxy, Tor, relay, bot, spam and known attacker, with last-seen dates) and the abuse contact of the network.

### Authentication

1. Get an API key from the [IPGeolocation.io dashboard](https://app.ipgeolocation.io/).
2. In Shuffle, authenticate the IPGeolocation app with:
   - **API key**: your key
   - **URL**: `https://api.ipgeolocation.io`

The app sends the key in the `X-IPGeolocation-API-Key` header, so it never appears in a request URL or in action results.

A free key covers IP location. Domain lookups, the bulk lookups, IP security, abuse contact and ASN need a paid plan; without it they return HTTP 401 with a message that says so.

### Actions

| Action | Input | What it returns |
| --- | --- | --- |
| Get IP location | `ip` (IP address or domain) | Location, ASN, network, company, time zone, currency. Options: `lang`, `include`, `fields`, `excludes` |
| Get IP location in bulk | body `{"ips": ["8.8.8.8", "1.1.1.1"]}` | The same for up to 50,000 IP addresses or domains |
| Get IP security | `ip` | Threat score (0-100) and VPN, proxy, residential proxy, Tor, relay, bot, spam, known attacker, cloud provider and corporate gateway signals. Labelled **Get IOC** |
| Get IP security in bulk | body `{"ips": [...]}` | The same for up to 50,000 IP addresses |
| Get abuse contact | `ip` | Abuse emails, phone numbers, organization and address of the network |
| Get abuse contact in bulk | body `{"ips": [...]}` | The same for up to 50,000 IP addresses |
| Get ASN by IP | `ip` | AS number and name, organization, type, registry, allocation date. `include` adds peers, upstreams, downstreams, routes, whois |
| Get ASN by number | `asn` (`AS15169` or `15169`) | The same, by AS number |

Credits are charged per IP address, also in bulk. See the [API documentation](https://ipgeolocation.io/documentation/ip-location-api.html).

### Good to know

- **Check `status`.** Shuffle marks an action as successful whenever the API answers, also with an error. Use `$<action>.status` (200 means data) and read `$<action>.body.message` otherwise: 401 invalid key or not in your plan, 423 bogon or private IP, 400 invalid input, 429 credit limit.
- **Never send a blank `ip` or `asn`.** Without one, the API looks up the address of the caller, which is your Shuffle server, and returns it as a normal result. Run the action only when the input is not empty, for example with a branch condition `$exec.ip` *is empty* (inverted).
- **Turn on `ssl_verify`.** Shuffle's generated actions do not verify TLS certificates unless `ssl_verify` is set to `true`.
- **A low threat score is not a "safe" verdict.** It means no risk signals were found. Missing data (an error status) is not a clean result either.

### Example cURL

```
curl -H 'X-IPGeolocation-API-Key: YOUR_API_KEY' \
     'https://api.ipgeolocation.io/v3/security?ip=171.25.193.25'
```
