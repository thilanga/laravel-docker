# laravel-docker

## Clone this repo

```bash
git clone git@github.com:thilanga/laravel-docker.git
cd laravel-docker
```

## Install the App

now, create the app in the directory named `app`

```bash
cd app
rm .gitkeep
docker run --user $(id -u) --rm -v $(pwd):/app composer/composer create-project --prefer-dist laravel/laravel .
```

### Configuration

To change configuration values, look in the `docker-compose.yml` file and change the `php` container's environment variables. These directly correlate to the Laravel environment variables.

### Build & Run

```bash
docker-compose up --build -d
docker exec -it docker_php_1 php ../artisan key:generate
```

Navigate to [http://localhost:80](http://localhost:80) and you should see the application home page

### Stop Everything

```bash
docker-compose down
```