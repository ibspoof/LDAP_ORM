LDAP ORM Examples
=================

Querying
----------

Find All Records with cn=TestGroup

```php
require('LDAP_ORM.php');

$ldap = new LDAP_ORM('example.com');
$ldap->bind('cn=admin,dc=example,dc=com', 'password');

$res = $ldap->find('all', array('conditions' => "cn=testGroup"), 'ou=Groups,dc=example,dc=com');
```

Alternatively the above query can be written using the findAllBy magic method:

```php
require('LDAP_ORM.php');

$ldap = new LDAP_ORM('example.com');
$ldap->bind('cn=admin,dc=example,dc=com', 'password');

$res = $ldap->findAllByCn("testGroup", 'ou=Groups,dc=example,dc=com');
```


Search by DN
-------------

```php
$res = $ldap->findByDn($dn);
```


Saving Record
--------------

```php
$dn = $ldap->dnBuilder(array('cn' => 'TestInsert2'), 'ou=People,dc=example,dc=com');
$data = array(
	'green' => 'fsdfsd',
	'sn' => 'Test Insert',
	'objectClass' => array('person', 'top')
);

$ldap->save($dn, $data, false);
```
With the last parameter = false, the default, any attribute being saved that does not exist in schema will be removed
from the data set before submitting to LDAP server avoiding errors.  Setting to true or 'strict mode' will return false
and set an error and will not submit to LDAP server.


Helpers
-------------

*Get/Show Error*

Get or Show Last Error

```php
$ldap->getError();
$ldap->showError(); // will auto echo with formatting
```


*DN Builder*

A helper function to build a DN based on a baseDN is included

```php
$dn = $ldap->buildDN(array('cn' => 'Test', 'ou'=>'Staff'), 'dc=example,dc=com');
```
Result: cn=Test,ou=Staff,dc=example,dc=com


