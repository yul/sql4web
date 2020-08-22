# SQL4Web
Programming API to work with web sites and web API's via database queries

# Idea
Instead of using API's directly, programmer requests data via local database. When data is missing, it is downloaded via corresponding API adapter.

# How it works
User:
- issues SELECT SQL query

SQL4Web:
- detects api's to use and data to fetch by table and field names
- queries data and saves response to local database (sqlite?)
- returns collected data in Python generator or iterator (possible streaming support)

User:
- creates session (transaction)
- sends UPDATE/INSERT/DELETE queries
- commits changes

SQL4Web:
- detects changes and sends queries to create/update/delete records
In order to detect changes, all records will have session id and deleted flag, for example, or maybe copy database and compare to find changes.

# Structure
- main engine to communicate with user
- adapters for each supported api
- scrapy scripts to access raw web sites

# Generic APIs
- collect swagger api description to create adapter on the fly
- Wordpress and other CMS/CRMs/frameworks may have generic API's available on any site that is built with those frameworks
- generic REST API, described with endpoints hash?

# Example

    from sql4web import require, select, insert, update, delete
    
    require([{"facebook": { ... credentials, api keys, options, etc. ...}, {"twitter": ...}]
    facebook_posts = select("SELECT post FROM facebook WHERE email=%s ORDER BY date DESC LIMIT 100", ["user@example.com"])
    twitter_posts = select("SELECT tweet FROM twitter WHERE email=%s ORDER BY date DESC LIMIT 100", ["user@example.com"])
    # OR JOIN
    all_posts = select("SELECT post, "facebook" source FROM facebook WHERE email=%s ORDER BY date DESC LIMIT 100 " \
                   "UNION SELECT tweet post, "twitter" source FROM twitter WHERE email=%s ORDER BY date DESC LIMIT 100", ["user@example.com"])
    
