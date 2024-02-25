# Landing page

![Website](https://img.shields.io/website?down_message=offline&up_message=online&url=https%3A%2F%2Ftothlp.hu)
![GitHub last commit (branch)](https://img.shields.io/github/last-commit/tothlp/tothlp.github.io/master?label=last%20released)
![GitHub deployments](https://img.shields.io/github/deployments/tothlp/tothlp.github.io/github-pages?label=state)

This repo hosts my Landing page. It uses [Jekyll](https://jekyllrb.com/) to create the static site, and Github Pages for hosting.
The site uses the [sproogen/modern-resume-theme](https://github.com/sproogen/modern-resume-theme) template, huge shoutout to its author, [James Grant](https://www.jameswgrant.co.uk).

## Running

Install the dependencies: 

```bash
bundle install
```

Run the server:

```bash
bundle exec jekyll serve
```

## Configuration options

The soul of the site lies within [_config.yml](_config.yml). In the following, you can see the different options.

### Site
```yaml
repository: tothlp/tothlp.github.io
favicon: images/favicon.ico
# Dark Mode (true/false/never)
darkmode: true
```
### Content configuration version
```yaml
version: 2
```
### Last update
```yaml
last_update: &last_update #date
```
### Personal info
```yaml
name: &author 
title: 
email: 
email_title: #title, or custom icon, eg.: <i class="far fa-envelope"></i>
phone: # or other info, like location
phone_title: #title, or custom icon, eg.: <i class="far fa-envelope"></i>
website: 
website_title:
```
### Social links
```yaml
twitter_username: 
github_username:  
#stackoverflow_username: ""
#dribbble_username: 
#facebook_username: 
#flickr_username: 
# instagram_username: 
# linkedin_username: 
#xbox_username: 
#xing_username: 
#pinterest_username: 
#youtube_username: 
#orcid_username: 0000-0000-0000-0000
#googlescholar_username: 
```
### Additional icon links
```yaml
additional_links: #custom links for the upper navigation
- title: Telegram
  icon: fab fa-telegram # Use fontawesome here
  url: 
```
### Google Analytics and Tag Manager
#### Using more than one of these may cause issues with reporting
```yaml
# gtm: ""
gtag: ""
# google_analytics: ""
```
### About Section
```yaml
about_title: Rólam
about_profile_image: images/profile.jpg
about_content: | # this will include new lines to allow paragraphs
content:
  - title: #Feel free to include html and icons: <small><i class="fas fa-tools"></i> </small>  Skills   <small><i class="fas fa-feather"></i></small>
    layout: # text or list
    content: |

  - title: Projektek # Title for the section
    layout: list # Type of content section (list/text)
    content:
      - layout: # center (not working) / left / right
      # border: weak #// or no value.
        title: 
        link: 
        #link_text: Link Text
        sub_title: 
        caption:
        description: |

        quote: >  
            > 
        additional_links: 
          - title:  Github repo
            icon: fab fa-github
            url: https://github.com
        
```
### Footer
```yaml
footer_show_references: true
references_title: *last_update
```
### Build settings
```yaml
#theme: modern-resume-theme
remote_theme: sproogen/modern-resume-theme

sass:
  sass_dir: _sass
  style: compressed

plugins:
 - jekyll-seo-tag

exclude : [
  "Gemfile",
  "Gemfile.lock",
  "node_modules",
  "vendor/bundle/",
  "vendor/cache/",
  "vendor/gems/",
  "vendor/ruby/",
  "lib/",
  "scripts/",
  "docker-compose.yml",
  "tothlp.github.io.code-workspace"
  ]
```