
PHP LDAP ORM
===========


About
-----------

LDAP ORM is a simple PHP Library that makes searching for records in LDAP and saving or updating easier.  It is inspired by CakePHP/Rails and supports both 'hasMany' and 'belongsTo' associations.  It also supports 'findBy{Attribute}' and 'findAllBy{Attribute}' functions for quick retrieval of records.

Feature List
-------------
- SASL Support (if proper LDAP PHP Libraries are Installed)
- findBy and findAllBy magic methods
- Single DN Result
- Save and Update with strict mode (check that attributes exist in schema before updating)
- 'hasMany' and 'belongsTo' support
- 'Find' supports: all, first, count, list
- Find conditions can use AND or OR between values ie. "cn=Test OR cn=Test2" and proper LDAP search formatting will be applied.
	- Note: Only one method can be used at a time but multiple of the same methods can be used "cn=Test AND cn=Test2 AND name=Test"
- Organizes results by the top most dn value
	- Example: results under a dn of ou=People,dc=example,dc=com would be namespaced in results under '[People]' array


Basic Usage
-------------
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

The results above both examples will return as

```php
(
   [0] => Array
        (
            [Groups] => Array
                (
                    [cn] => testGroup
                    [objectclass] => Array
                        (
                            [0] => groupOfNames
                            [1] => top
                        )
                    [member] => Array
                        (
                            [0] => cn=TestUser,ou=People,dc=example,dc=com
                            [1] => cn=Test3,ou=People,dc=example,dc=com
                        )
                    [dn] => cn=testGroup,ou=Groups,dc=example,dc=com
                )
            )
        )
   )
```



Advanced Usage
----------------
To search using the hasMany association such as finding a Group where cn=testGroup and find all the People that are listed in the members multi-attribute use the following format:

```php
require('LDAP_ORM.php');

$ldap = new LDAP_ORM('example.com');
$ldap->bind('cn=admin,dc=example,dc=com', 'password');

$res = $ldap->find('first', array('conditions' => "cn=testGroup"
	'hasMany' => array(
        'on' => array('member' => 'dn'),
        'fields' => array(),
        'base' => 'ou=People,dc=example,dc=com')
    ), 'ou=Groups,dc=example,dc=com');
```

the 'on' array uses the format array('{FoundResultAttribute}' => '{AttributeToSearchForInJoinedResults}')

the Result of this would look like:

```php
Array
(
    [0] => Array
        (
            [Groups] => Array
                (
                    [cn] => testGroup
                    [objectclass] => Array
                        (
                            [0] => groupOfNames
                            [1] => top
                        )
                    [member] => Array
                        (
                            [0] => cn=TestUser,ou=People,dc=example,dc=com
                            [1] => cn=Test3,ou=People,dc=example,dc=com
                        )
                    [dn] => cn=testGroup,ou=Groups,dc=example,dc=com
                )
            [People] => Array
                (
                    [0] => Array
                        (
                            [sn] => Test
                            [cn] => TestUser
                            [objectclass] => Array
                                (
                                    [0] => person
                                    [1] => top
                                )
                            [dn] => cn=TestUser,ou=People,dc=example,dc=com
                        )
                    [1] => Array
                        (
                            [cn] => Test3
                            [objectclass] => Array
                                (
                                    [0] => person
                                    [1] => top
                                )
                            [sn] => dsfsdfsd
                            [dn] => cn=Test3,ou=People,dc=example,dc=com
                        )
                )
        )
)
```


to use the BelongsTo option:

```php
require('LDAP_ORM.php');

$ldap = new LDAP_ORM('example.com');
$ldap->bind('cn=admin,dc=example,dc=com', 'password');

$g = $ldap->find('all', array('conditions' => "cn=Test2",
    'belongsTo' => array(
        'on' => array('groupdn' => 'dn'),
        'base' => 'ou=Groups,dc=example,dc=com')
), 'ou=People,dc=example,dc=com');
```


More Examples
------------
See [Examples.md](Examples)

License
---------
LDAP ORM is Licensed under the Creative Commons Attribution 3.0 license
http://creativecommons.org/licenses/by/3.0/us/


Issues
---------
Please use GitHub Issues to submit any problems found

