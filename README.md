# laravel-docker

## Clone this repo

```bash
git clone git@github.com:thilanga/laravel-docker.git
cd laravel-docker
```
### Configuration

To change configuration values, look in the `docker-compose.yml` file and change the `php` container's environment variables. These directly correlate to the Laravel environment variables.

### Build & Run

```bash
docker-compose -f docker/docker-compose.yml up --build -d
docker exec -it --user www-data docker_app_1 composer create-project --prefer-dist laravel/laravel . 
```

Navigate to [http://localhost](http://localhost) and you should see the application home page

### Stop Everything

```bash
docker-compose down
```
