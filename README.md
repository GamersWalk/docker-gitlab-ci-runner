# Docker GitlabCI Runner images

Gitlab CI runner docker base images with ssh-key sharing.

This repo is based on [bobey/docker-gitlab-ci-runner](https://github.com/bobey/docker-gitlab-ci-runner) repository.
## Table of contents

- [PHP Images](#php-images)
  - [Usage](#usage-1)
  - [Custom PHP configuration](#custom-php-configuration)
  - [Development](#development)
- [THANKS](#thanks)


## PHP Images

We provide docker gitlab-ci runner images for PHP 7 containing the following stack:

- PHP 7.x
- Git
- Composer
- NodeJS

### Usage

You can run as many runners as you want by executing:

```
docker run \
  -e CI_SERVER_URL=https://ci.example.com \
  -e REGISTRATION_TOKEN=replaceme \
  -e HOME=/root \
  -e GITLAB_SERVER_FQDN=gitlab.example.com \
  -v /absolute/path/to/your/home/.ssh/id_rsa:/root/.ssh/id_rsa:ro \
  bobey/docker-gitlab-ci-runner-php7.0
```

In your GitlabCI, a basic phpunit job could looks like this:

```
composer install
php vendor/phpunit/phpunit/phpunit --coverage-text
```

By displaying code coverage as text, you can easily extract code coverage metrics. In your project settings, under
"Test coverage parsing", just input the following regex: `  Lines:\s+(\d+.\d+\%)`.

### Custom PHP configuration

All PHP images comes with the following extensions pre-installed but not enabled by default with the exception of
`xdebug` and `zmq`:

- memcache.so
- mongo.so
- redis.so
- ssh2.so
- xdebug.so
- zmq.so

If you need to customize the php configuration, you can add your own settings to the `ci-runner.ini` file.
For example, the following command in one of your gitlab-ci jobs will enable `mongo.so` extension:

```
echo 'extension="mongo.so"' >> ~/.phpenv/versions/$(phpenv version-name)/etc/conf.d/ci-runner.ini
```

You can of course customize any other parameter of the `php.ini` configuration file. Below command will set
`Europe/Paris` as the default timezone:

```
echo 'date.timezone="Europe/Paris"' >> ~/.phpenv/versions/$(phpenv version-name)/etc/conf.d/ci-runner.ini
```

### Composer and Github API rate limit

All PHP images comes with Composer pre-installed and ready to be used. But, as you might already know, Github API rate
limit is quite often reached when building projects in CI. See what [Composer doc say about it](https://getcomposer.org/doc/articles/troubleshooting.md#api-rate-limit-and-oauth-tokens).

One way to handle this problem is to create an `auth.json` file and share it with your gitlab-ci-runners via a volume:

```json
{
    "http-basic": {},
    "github-oauth": {
        "github.com": "GITHUB_GENERATED_TOKEN"
    }
}
```

Then, start your runner with an extra `-v` option:

```
docker run \
  -e ...
  -v /absolute/path/to/composer-auth.json:/root/.composer/auth.json:ro \
  bobey/docker-gitlab-ci-runner-php7.0
```

### Development

This docker image is based on bobey/docker-gitlab-ci-runner image. In order to build it, you need to execute the
following command:

```
docker build -t gamerswalk/docker-gitlab-ci-runner-php7.0 github.com/gamerswalk/docker-gitlab-ci-runner/php/7.0
```

## THANKS

**Remember this repo is based on [bobey/docker-gitlab-ci-runner](https://github.com/bobey/docker-gitlab-ci-runner)
repository**.

