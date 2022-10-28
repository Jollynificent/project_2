##Documentation of Lemp Stack implemenatation

#update package index and install Nginx
`sudo apt update`
`sudo apt install nginx -y`

#verify installation
`sudo systemctl status nginx`

#check for access
`curl http://127.0.0.1:80`
![alt text](./images/nginx status.PNG)

#install mysql
`sudo apt install mysql-server -y`

#log into console, set password and run security script
`sudo mysql`

#interactive script
`sudo mysql_secure_installation`

#test login to console 
`sudo mysql -p`
exit

#install php
`sudo apt install php-fpm php-mysql`

#create web directory and assign ownership
`sudo mkdir /var/www/projectLEMP`
`sudo chown -R $USER:$USER /var/www/projectLEMP`

#create a config fie and add the lines
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

save with ":wq!:"

#link config file 
`sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`

#test for syntax errors
`sudo nginx -t`

#disable default Nginx host and reload Nginx to apply changes
`sudo unlink /etc/nginx/sites-enabled/default`
`sudo systemctl reload nginx`

#Create index.html file in locationto test
`sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`
![alt text](./images/LEMP status.PNG)

#Open new file info.php 
`sudo nano /var/www/projectLEMP/info.php`
   #insert the lines
<?php
phpinfo();
"esc" and "wq!"

#reload nginx
`sudo systemctl reload nginx`
![alt text](./images/php status.PNG)

#remove file created
`sudo rm /var/www/projectLEMP/info.php`

#connect mysql console
`sudo mysql`
   #run on console
CREATE DATABASE `example_database`;
CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
GRANT ALL ON example_database.* TO'example_user'@'%';
exit;

#test for proper permissions
`mysql -u example_user -p`
    #run
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

exit;

#create php file
`nano /var/www/projectLEMP/todo_list.php`
   #insert content
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
":wq!"
![alt text](./images/todo status.PNG)
