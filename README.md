# ratelimited.dev
A simple public dummy API with rate-limited endpoints for testing.

## Motivation

There are a number of good dummy APIs for use when testing new things in their early stages, but they're either not rate-limited, or the rate limits aren't published or are unclear.

This API seeks to provide a simple set of rate-limited endpoints, limited either by `IP Address` or by a user-defined `Key`.

## Usage

### By IP Address

These endpoints will be rate limited using the caller's IP address as the differentiating token. If a given IP exceeds the rate limit for the given endpoint, you should get back a `429 - Too Many Requests` response.

The general format is:

```
curl https://api.ratelimited.dev/ip/<requests-per-second>
```

The specific endpoints available (for now) are:

- https://api.ratelimited.dev/ip/1-per-second
- https://api.ratelimited.dev/ip/2-per-second
- https://api.ratelimited.dev/ip/5-per-second
- https://api.ratelimited.dev/ip/10-per-second
- https://api.ratelimited.dev/ip/20-per-second
- https://api.ratelimited.dev/ip/50-per-second
- https://api.ratelimited.dev/ip/100-per-second
- https://api.ratelimited.dev/ip/200-per-second
- https://api.ratelimited.dev/ip/500-per-second
- https://api.ratelimited.dev/ip/1000-per-second
- https://api.ratelimited.dev/ip/2000-per-second
- https://api.ratelimited.dev/ip/5000-per-second
- https://api.ratelimited.dev/ip/1-per-minute
- https://api.ratelimited.dev/ip/2-per-minute
- https://api.ratelimited.dev/ip/5-per-minute
- https://api.ratelimited.dev/ip/10-per-minute
- https://api.ratelimited.dev/ip/20-per-minute
- https://api.ratelimited.dev/ip/50-per-minute
- https://api.ratelimited.dev/ip/100-per-minute
- https://api.ratelimited.dev/ip/200-per-minute
- https://api.ratelimited.dev/ip/500-per-minute
- https://api.ratelimited.dev/ip/1000-per-minute
- https://api.ratelimited.dev/ip/2000-per-minute
- https://api.ratelimited.dev/ip/5000-per-minute

### By Key

To simulate scenarios where a given _account_ is rate limited, where the quota could be shared across multiple instances, you can use the `key` option. Any requests across any number of machines that share the same `key` will also share the same rate limit quota.

To use this option, hit the endpoints below, and also include the _alphanumeric_ key you'd like to use (can also have hyphens) in the header `key`.

```
curl -H "key: shared-across-instances" https://api.ratelimited.dev/key/<requests-per-second>
```

The endpoints are:

- https://api.ratelimited.dev/key/1-per-second
- https://api.ratelimited.dev/key/2-per-second
- https://api.ratelimited.dev/key/5-per-second
- https://api.ratelimited.dev/key/10-per-second
- https://api.ratelimited.dev/key/20-per-second
- https://api.ratelimited.dev/key/50-per-second
- https://api.ratelimited.dev/key/100-per-second
- https://api.ratelimited.dev/key/200-per-second
- https://api.ratelimited.dev/key/500-per-second
- https://api.ratelimited.dev/key/1000-per-second
- https://api.ratelimited.dev/key/2000-per-second
- https://api.ratelimited.dev/key/5000-per-second
- https://api.ratelimited.dev/key/1-per-minute
- https://api.ratelimited.dev/key/2-per-minute
- https://api.ratelimited.dev/key/5-per-minute
- https://api.ratelimited.dev/key/10-per-minute
- https://api.ratelimited.dev/key/20-per-minute
- https://api.ratelimited.dev/key/50-per-minute
- https://api.ratelimited.dev/key/100-per-minute
- https://api.ratelimited.dev/key/200-per-minute
- https://api.ratelimited.dev/key/500-per-minute
- https://api.ratelimited.dev/key/1000-per-minute
- https://api.ratelimited.dev/key/2000-per-minute
- https://api.ratelimited.dev/key/5000-per-minute

## Additional Details

### NGINX

NGINX is used under the hood to provide the rate limiting functionality. They use the Leaky Bucket algorithm, with optional values available. For more details on what you can do with NGINX, check out their documentation. This blog is a good place to start: https://www.nginx.com/blog/rate-limiting-nginx/

Our configuration follows this pattern:
```
limit_req zone=ip_1rs burst=1 nodelay;
```

Our rate limits are all defined as _request per second_ limits. When applied, we always set the burst to the number of requests per second (1 r/s --> 1 burst, 20 r/s --> 20 burst, etc.). And we set `nodelay` so that your requests aren't queued, but are rather returned immediately if they satisfy the given rate limit. (This paragraph makes more sense if you know how NGINX handles rate limits).

Hopefully soon I'll post the actual code, which at this point is just a simple NGINX configuration file. For now, this is all you get.

### Limited Compute Power

Since this isn't a high priority project, this API has limited compute power in the cloud to reduce costs. As such, the bigger rate limits above may not work with precision. Also, please be kind and don't blast the system expecting it to handle everything.

## NO WARRANTY

Don't expect perfection from this API. Use at your own risk.
