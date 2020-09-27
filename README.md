<?php
require_once 'Zend/Loader.php';
require_once 'Zend/Db.php';

$params = array( 'host' => 'root', 'username' => 'root', 'password' => 'localhost', 'dbname' => 'test' );

$db = Zend_Db::factory('Mysql', $params);

try {
    $db->getConnection();
} catch (Zend_Db_Adapter_Exception $e) {
} catch (Zend_Exception $e) {
}
$db->query("set names utf8");

$tables = $db->listTables();
print_r( $tables );

$db->beginTransaction();

$db->insert( 'testtable', array( 'groupid' => '30', 'name' => "o'reilly", 'desc' => 0 ) );

echo $db->lastInsertId() . "\n<br/>";

$db->rollBack();
//$db->commit();

$count = $db->fetchOne( "SELECT COUNT(*) FROM testtable" );
var_dump( $count );

$res = $db->fetchAll( "SELECT * FROM testtable" );
print_r( $res );

$res2 = $db->fetchAll( $db->select()->from( 'testtable' ) );
print_r( $res2 );

$sql = $db->select()
	->from( array( 'tt' => 'testtable' ) )
	->join( array( 'gt' => 'groups' ),
		'tt.groupid=gt.id',
		array( 'name' ) );
$result = $db->query( $sql )->fetchAll();
print_r( $result );

$stmt = $db->query( $db->select()->from('testtable') );
while ( $row = $stmt->fetch() ) { print_r( $row ); }

$stmt = $db->prepare( $db->select()->from( 'testtable' ) );
$stmt->execute();
$count = $stmt->rowCount();
var_dump( $count );

var_dump( $db->describeTable( 'testtable' ) );
?>
