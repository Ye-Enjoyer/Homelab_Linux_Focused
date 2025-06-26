## Installation

Docker compose using provided directory structure 

Create a new directory called glance as well as the template files within it by running:

```bash

    mkdir glance && cd glance && curl -sL https://github.com/glanceapp/docker-compose-template/archive/refs/heads/main.tar.gz | tar -xzf - --strip-components 2

```

Then, edit the following files as desired:

  docker-compose.yml to configure the port, volumes and other containery things
  config/home.yml to configure the widgets or layout of the home page
  config/glance.yml if you want to change the theme or add more pages

Other files you may want to edit

When ready, run:
```bash
docker compose up -d

```

If you encounter any issues, you can check the logs by running:

```bash

docker compose logs

```

### Inhalt der home.yml

```yml

- name: Startpage
  width: slim
  hide-desktop-navigation: true
  center-vertically: true
  columns:
    - size: full
      widgets:
        - type: search
          autofocus: true

        - type: monitor
          cache: 1m
          title: Services
          sites:
            - title: Nextcloud
              url: https://bataraycloud.duckdns.org/login
              icon: /assets/nextcloud.png
            - title: Romm
              url: http://192.168.2.173:82/
              icon: /assets/romm.png


        - type: bookmarks
          groups:
            - title: General
              links:
                - title: Protonmail
                  url: https://account.proton.me/mail
                - title: Spotify
                  url: https://open.spotify.com/
                - title: Github
                  url: https://github.com/
            - title: Entertainment
              links:
                - title: YouTube
                  url: https://www.youtube.com/
                - title: Mangareader
                  url: https://mangareader.to/
                - title: Twitch
                  url: https://www.Twitch.com/
            - title: Social
              links:
                - title: Reddit
                  url: https://www.reddit.com/
                - title: Twitter
                  url: https://twitter.com/
                - title: Instagram
                  url: https://www.instagram.com/
