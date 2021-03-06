
Object DataMapper
===============
The Object DataMapper allowed for the simple mapping of properties from one object to another.
Some of the handy features it gives you include the expansion of object property strings and
the aliasing of property values.

```php
<?php
include_once 'lib/DataMapper.php';
include_once 'lib/ExpandObject.php';
include_once 'lib/MapItem.php';

$classOne = new stdClass();
$classOne->foo = 'test';

$dm = new DataMapper();
$dm->configure(array(
	'first_map' => new MapItem(array(
		'map:input' => array($classOne,'foo')
	))
));
$dm->execute();

echo 'Should be "test": '.$dm->first_map;
?>
```
This also could potentially be useful if you're pulling data into a model and want to remap it every time:

```php
<?php
include_once 'lib/DataMapper.php';
include_once 'lib/ExpandObject.php';
include_once 'lib/MapItem.php';

class UserModel {
	
	private $_map 		= null;

	public function __construct()
	{
		$this->_map = new DataMapper();
		$this->_map = $this->_map->configure(array(
			'title'	=> new MapItem(array(
				'map:input' 	=> 'details->name',
				'map:output'	=> 'username',
				'alias'			=> 'uname'
			))
		));
	}
	
	public function fetchUsers()
	{
		// Make us some sample users...
		$users = array();
		for($i=0;$i<5;$i++){
			$users[]=(object)array('details' => (object)array(
					'name' => 'user'.$i
				)
			);
		}
		
		$results = array();
		$dm 	 = new DataMapper();
		foreach($users as $key => $user){
			// a sample class...
			$target = new stdClass();
			
			$results[$key] = $this->_map
				->setSource($user)
				->setTarget($target)
				->execute();
		}
		
		return $results;
	}
}

$um 	= new UserModel();
$users 	= $um->fetchUsers();
foreach($users as $user){
	echo 'from $user::details->name: '.$user->username."\n";
}
?>
```