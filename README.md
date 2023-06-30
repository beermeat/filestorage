## Image storage service

Nginx + Minio + Imgproxy

# Install

    git clone git@github.com:beermeat/filestorage.git
    cd filestorage
    ln -s docker/docker-compose.dev.yml docker-compose.yml
    cp docker/.env.dist .env
    docker compose up -d

# Imgproxy crop url examples (see presets.example.conf)

    curl http://127.0.0.1:8080/i/200x200/cat.jpg
    curl http://127.0.0.1:8080/i/200x200/cat.jpg
    curl http://127.0.0.1:8080/i/400x400-fit/cat.jpg

# Get origin file (via nginx secure link module)

    curl http://127.0.0.1:8080/o/cat.jpg?sign=phWwxR_5LWYgQ_FKnVu8Xw&expires=9223372036854775807

```php
function signLink(string $url, string $secret, int $expires): string
{
    $path = parse_url($url)['path'];

    $md5 = md5("$path $secret $expires", true);
    $md5 = base64_encode($md5);
    $md5 = strtr($md5, ['+' => '-', '/' => '_', '=' => '']);

    return $url . '?sign=' . $md5 . '&expires=' . $expires;
}

echo signLink("http://127.0.0.1:8080/o/cat.jpg", "nginxsupersecret", PHP_INT_MAX);
// http://127.0.0.1:8080/o/cat.jpg?sign=phWwxR_5LWYgQ_FKnVu8Xw&expires=9223372036854775807
```

    curl http://127.0.0.1:8080/o/cat.jpg?sign=phWwxR_5LWYgQ_FKnVu8Xw&expires=9223372036854775807

