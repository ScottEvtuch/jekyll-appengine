
# Jekyll on Google App Engine #

This repo contains an example Jekyll site configured to run on Google App Engine and be built automatically using Google Cloud Build. Since Jekyll sites are entirely static files we can host these sites very cheaply, if not for free, using App Engine. The only time an instance is spawned is to serve fancy custom 404 pages.

Check out [this guide](https://queensidecastle.com/guides/jekyll-on-app-engine-with-ci-cd) on how to use this method on my blog.

## Usage ##

### Cloud Build deployment ###

Set up a trigger in Cloud Build to run the cloudbuild.yaml file after a commit that you want published. This should be done in the same project where your App Engine app exists.

### Manual Deployment ###

You can also manually deploy the site to App Engine using the Google Cloud SDK and running "gcloud app deploy" from the root of the repository.

## Estimated Costs ##

A single site hosted using this method in Google Cloud Platform should never go above the limits for free tier, and thus should not incur any charges whatsoever. If you've already surpassed the free tier limits then the costs entirely depend on your rate of 404 errors.

Assuming using a single B1 instance at a rate of $0.05/hr and that 404s are evenly distributed accross time:

| 404 Rate   | Monthly Cost (without free tier) |
| ---------- | -------------------------------- |
| 1 / min    | $36.60                           |
| 30 / hour  | $18.30                           |
| 15 / hour  | $9.15                            |
| 1 / hour   | $0.61                            |
| 8 / day    | $0.20                            |
| 1 / day    | $0.03                            |

## Scaling Method ##

Depending on your use-case it might be better to use automatic scaling instead of basic scaling. If you suspect that your app will receive a lot of traffic **and** you are below the free tier limits, automatic scaling will be less expensive. App Engine allows 28 instance-hours of automatic scaling instances but only 8 instance-hours of basic scaling instances under free tier.

```
instance_class: F1
automatic_scaling:
  max_instances: 1
```

## 404 Pages ##

If you don't care about friendly 404 pages and want to completely prevent App Engine from ever spawning an instance and incuring costs, you can replace the "Catch-all" handler with the handler below. I suspect that Google might at some point crack down on App Engine apps that never spawn an instance, but it doesn't appear they have done so yet.

```
# Return 404
- url: /.*
  static_files: fakepath
  upload: _site/.*
  require_matching_file: false
  secure: always
  redirect_http_response_code: 301
```
