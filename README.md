# PHP WebDev 2019

La théorie du cours de PHP pour les étudiants en première année du **BES Web developer**

## PDO

PDO (PHP Data Objects) est une extension officielle du language PHP. Il s'agit d'une interface d'accès à une base de données depuis PHP. Il s'agit de la façon recommandée de communiquer avec une base de données depuis la version 5.1 de PHP (La dernière version stable de PHP au jours d'aujourd'hui en mars 2019 est la version 7.3).

PDO permet d'abstraire l'accès aux données. En d'autres termes, vous devez l'utiliser de la même façon quelque soit le type de base de donnée à laquelle vous vous connectez.

Dans le cadre de ce cours nous connecterons PDO à une base de donnée MySQL telle que précédemment vue au cours de SGBD.

### Connection de PDO

PDO est un Objet... Notion que vous ne verrez pas dans ce cours... Lorsque l'on se connecte à PDO, on crée une **Instance de l'objet PDO**
Une connection a PDO ne doit être effectuée qu'une seule fois dans un script, même si celui-ci effectue plusieurs appels vers la base de donnée. (autrement plusieurs instances de connexion vont être créées et ralentir votre serveur de base de données.)
L'exemple le plus simple de connection à PDO est le suivant :

```php
<?php
$user = "root";
$pass = "";
$pdo = new PDO('mysql:host=localhost;dbname=test;port=3306', $user, $pass);
?>
```

Où l'on spécifie les données spécifiques de connection à la base de donnée (_host_, _dB name_ et _port_), ainsi que le nom d'utilisateur et le mot de passe.

Cependant, cette manière de faire n'est pas recommandée, parce que en cas d'erreur (base de données temporairement inaccessible par example), PHP va envoyer un message d'erreur à l'utilisateur qui contiendra notamment le nom d'utilisateur et le mot de passe de la BdD !

C'est pourquoi lors de votre connexion à la DB vous devez capturer les erreurs grâce à un bloc de code **Try - Catch** tel que montré ci-dessous.

```php
<?php
$user = "root";
$pass = "";
try {
    $pdo = new PDO('mysql:host=localhost;dbname=test;port=3306', $user, $pass);
} catch (PDOException $e) {
    print "<p>Erreur de connection !</p>";
    exit();
}
?>
```

pour fermer la connexion vers la base de données, il vous suffit de changer la variable PDO en **null** :

```php
$pdo = null;
```

## Effectuer une requête vers la base de données

### Méthode query

Dans le cadre d'une requête où vous n'utilisez pas de **variable**, vous pouvez utiliser la **méthode** (nom donné à une **fonction** dans un objet) **query** (traduction: requête). puis utiliser pour chaque rangée du résultat obtenu la méthode **fetch**(traduction: récupérer)

```php
$stmt = $pdo->query('SELECT * FROM users');
while ($row = $stmt->fetch())
{
    echo $row . "<br>";
}
```

Un autre example avec un _for each_ :

```php
$stmt = $pdo->query('SELECT name FROM users');
foreach ($stmt as $row)
{
    echo $row['name'] . "\n";
}
```

### Méthode Prepared statement

Dans le cadre où vous utilisez des variables dans votre requête, vous êtes **OBLIGES** d'utiliser les méthodes **prepare** et **execute** afin de protéger votre base de données contre les [injections SQL](https://fr.wikipedia.org/wiki/Injection_SQL).

Vous devez d'abord préparer la requête à exécuter en prévenant PHP du nombre de variables que vous voulez entrer, puis l'exécuter en passant en argument un tableau comprenant les éléments à ajouter à la requête.

Il existe deux manières légèrement différentes d'exécuter un prepared statement en PHP, choisissez celle que vous voulez !

1. Façon 1

```php
$stmt = $pdo->prepare('SELECT * FROM users WHERE email = ? AND status=?');
$stmt->execute([$email, $status]);
$user = $stmt->fetch();
```

1. Façon 2

```php
$stmt = $pdo->prepare('SELECT * FROM users WHERE email = :email AND status=:status');
$stmt->execute(['email' => $email, 'status' => $status]);
$user = $stmt->fetch();
```

En cas d'update ou de delete dans la base de données, le principe est exactement le même !

```php
$sql = "UPDATE users SET name = ? WHERE id = ?";
$pdo->prepare($sql)->execute([$name, $id]);
```

## A vous

Vous en savez assez pour utiliser PDO dans vos propres programmes.

Maintenant ajoutez une connexion à une base de données à votre jeu du pendu pour récupérer un dictionnaire.
Ensuite, permettez aux utilisateurs de votre jeu d'ajouter des mots dans la base de données !

## Références

[Documentation officielle PHP](http://php.net/manual/fr/)

[(The only proper) PDO tutorial](https://phpdelusions.net/pdo)
