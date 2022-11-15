1. Time to create our Laravel project, run `docker-compose run --rm composer create-project --prefer-dist laravel/laravel:^8.0 . `, in your root project's folder

2. Open `src/.env` in your editor and change the configuration lines for the database connection as follows

    DB_DATABASE=homestead

    DB_USERNAME=homestead

    DB_PASSWORD=secret

3. Run the project, `docker-compose up mysql server php` and access it on http://localhost:8000