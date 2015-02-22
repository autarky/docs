# Database

Autarky comes with a very small set of classes to manage PDO instances. The manager will read your `config/database` config file and translate those into lazily loaded PDO objects. You can retrieve a connection either via the ConnectionManager:

	$manager = $container->resolve('Autarky\Database\ConnectionManager');
	$manager->getPdo(); // get the default connection
	$manager->getPdo('other_connection');

The default connection is defined by the `connection` key in the config file.

Each connection must correspond to a key in the `connections` array in the config file. The structure of these connection arrays vary between different PDO drivers. The only one that always has to be present is the `driver` key, which should be the name of a [PDO driver](http://php.net/manual/en/pdo.drivers.php).

There is also the optional `pdo_options` array, which contain additional PDO options, some of which are described [here](http://php.net/manual/en/pdo.setattribute.php).

Lastly there is the optional `pdo_init_commands` array, which should be an array of strings containing SQL commands that are run right after the connection has been extablished.

In addition, any array keys not specifically mentioned here will simply be appended to the DSN. You can look up available DSN key/value pairs on the PDO driver page on php.net. For example, with Postgres, if you want to set the charset when connection, your PDO DSN should look like this:

	new PDO("host=localhost;options='--client_encoding=UTF8'", $user, $pass);

To emulate this with the connection config, simply add the following to a Postgres connection array:

	'options' => "'--client_encoding=UTF8'",

### SQLite

SQLite only requires a `path` key, which should be the full path to the SQLite database file.

### Postgres (pgsql)

You can specify `dbname`, `username`, `password`, `host` and `port`.

### MSSQL (sqlsrv)

`Database`, `username` and `password` should be specified. If you need to specify a port, simply add a comma behind the `Server` value:

	'Server' => 'localhost,1521'

### MySQL

You can specify `host`, `port`, `dbname`, `username`, `password` and `charset`.

### Other drivers

Other drivers may or may not work. Try to pass an array of all the DSN key/value pairs as specified by the php.net documentation.
