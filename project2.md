Documentation of project_2
`sudo apt update`
`sudo apt install nginx`
`curl http://127.0.0.1:80`
![alt text](./images/nginx status.PNG)
`sudo apt install mysql-server`
`sudo mysql`
`sudo apt install php-fpm php-mysql`
y
`sudo mkdir /var/www/projectLEMP`
`sudo chown -R $USER:$USER /var/www/projectLEMP`
`sudo nano /etc/nginx/sites-available/projectLEMP`
server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}

save
`sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`
`sudo nginx -t`
`sudo unlink /etc/nginx/sites-enabled/default`
`sudo systemctl reload nginx`
`sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`
![alt text](./images/LEMP status.PNG)
`sudo nano /var/www/projectLEMP/info.php`
<?php
phpinfo();
save
`sudo systemctl reload nginx`
![alt text](./images/php status.PNG)
`sudo rm /var/www/projectLEMP/info.php`
`sudo mysql`
CREATE DATABASE `example_database`;
CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
GRANT ALL ON example_database.* TO'example_user'@'%';
exit
`mysql -u example_user -p`
SHOW DATABASES;

CREATE TABLE example_database.todo_list (
item_id INT AUTO_INCREMENT,
content VARCHAR(255),
PRIMARY KEY(item_id)
);

INSERT INTO example_database.todo_list (content) VALUES 
("My first important item"),
("My second important item"),
("My third important item"),
("and this one more thing");

SELECT * FROM example_database.todo_list;

exit
`nano /var/www/projectLEMP/todo_list.php`
<?php
$user = "example_user";
$password = "password";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
save
![alt text](./images/todo status.PNG)
