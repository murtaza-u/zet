# NGINX - trim .html from the end of URLs

If you happen to run a statically generated website like me, one thing
you might find annoying is the .html extension at end of every URL.
Well, it seems like `nginx` has an easy configuration fix for this.

```
location / {
    if ($request_uri ~ ^/(.*)\.html) {
        return 301 /$1$is_args$args;
    }

    try_files $uri $uri.html $uri/ =404;
}
```

After editing, make sure to reload `nginx`,

```
sudo systemctl reload nginx
```

    #nginx
