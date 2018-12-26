## Test Repo for CaptainDuckDuck V1 One Click Apps

Process:
- Find/create a docker-compose file for the one click app
- Convert yaml file to json
- Copy the content into the structure as below
- Variables are prefixed with `$$cap`
- There is one special variable called `$$cap_appname$$` which exists for all oneclick apps
- Varibles can be anywhere in the JSON and they will be replaced by what user enters
- Each variable must have `id`, `type`, `label`. It may also have `defaultValue`, `validRegex`, `description`.
- Other than `image`, `environment`, `ports`, `volumes`, `depends_on`, other parameters are ignored. Make sure they are not important.

```
{
    "captainVersion": "1",
    "documentation": "Taken from https://docs.docker.com/compose/wordpress/ port mapping removed from WP as it's not needed",
    "dockerCompose":
    {
        "version": "3.3",
        "services":
        {
            "db":
            {
                "image": "mysql:$$cap_mysql_version",
                "volumes": [
                    "db_data:/var/lib/mysql"
                ],
                "restart": "always",
                "environment":
                {
                    "MYSQL_ROOT_PASSWORD": "$$cap_db_pass",
                    "MYSQL_DATABASE": "wordpress",
                    "MYSQL_USER": "$$cap_db_user",
                    "MYSQL_PASSWORD": "$$cap_db_pass"
                }
            },
            "wordpress":
            {
                "depends_on": [
                    "$$cap_appname$$-db"
                ],
                "image": "wordpress:$$cap_wp_version",
                "restart": "always",
                "environment":
                {
                    "WORDPRESS_DB_HOST": "$$cap_appname$$-db:3306",
                    "WORDPRESS_DB_USER": "$$cap_db_user",
                    "WORDPRESS_DB_PASSWORD": "$$cap_db_pass"
                }
            }
        },
        "volumes":
        {
            "db_data":
            {}
        }
    },
    "variables": [
    {
        "id": "$$cap_db_user",
        "label": "Database user",
        "defaultValue": "wordpressuser",
        "type": "text",
        "testRegex": "/^[a-z0-9\\-]+$/"
    },
    {
        "id": "$$cap_db_pass",
        "label": "Database password",
        "description": "",
        "type": "text",
        "validRegex": ".{22,}"
    },
    {
        "id": "$$cap_wp_version",
        "label": "WordPress Version",
        "defaultValue": "latest",
        "description": "Checkout their docker page for the valid tags",
        "type": "text"
    },
    {
        "id": "$$cap_mysql_version",
        "label": "MySQL Version",
        "defaultValue": "latest",
        "description": "Checkout their docker page for the valid tags",
        "type": "text"
    }]

}
```