---
title: 在ci modle中使用on duplicate key update
tags:
  - ci
categories:
  - Web development
toc: false
date: 2015-11-17 11:12:11
description:
feature:
---
Was wondering if this could be added to the active record library its a very useful function for some sites.

http://dev.mysql.com/doc/refman/5.0/en/insert-on-duplicate.html
<!-- more -->
Got Bored so here goes some code, added the following code chunks to the following files:
``` php
/system/database/drivers/mysql/mysql_driver.php

/**
* ON DUPLICATE UPDATE statement
*
* Generates a platform-specific on duplicate key update string from the supplied data
*
* @author    Chris Miller <chrismill03@hotmail.com>
* @since     1.6.2
* @access    public
* @param     string   the table name
* @param     array    the update/insert data
* @return    string
*/
function _duplicate_insert($table, $values)
{
    $updatestr = array();
    $keystr    = array();
    $valstr    = array();
    
    foreach($values as $key => $val)
    {
        $updatestr[] = $key." = ".$val;
        $keystr[]    = $key;
        $valstr[]    = $val;
    }
    
    $sql  = "INSERT INTO ".$this->_escape_table($table)." (".implode(', ',$keystr).") ";
    $sql .= "VALUES (".implode(', ',$valstr).") ";
    $sql .= "ON DUPLICATE KEY UPDATE ".implode(', ',$updatestr);
    
    return $sql;
}
and then the active record part:
system/database/DB_active_rec.php
/**
* On Duplicate Key Update
*
* Compiles an on duplicate key update string and runs the query
* 
* @author    Chris Miller <chrismill03@hotmail.com>
* @since     1.6.2
* @access    public
* @param     string    the table to retrieve the results from
* @param     array     an associative array of update value
* @return    object
*/
function on_duplicate($table = '', $set = NULL )
{
    if ( ! is_null($set))
    {
        $this->set($set);
    }
    if (count($this->ar_set) == 0)
    {
            if ($this->db_debug)
        {
                return $this->display_error('db_must_use_set');
        }
            return FALSE;
    }
    if ($table == '')
    {
        if ( ! isset($this->ar_from[0]))
        {
            if ($this->db_debug)
            {
                return $this->display_error('db_must_set_table');
            }
            return FALSE;
        }
        
        $table = $this->ar_from[0];
    }
    
        
    $sql = $this->_duplicate_insert($this->_protect_identifiers($this->dbprefix.$table), $this->ar_set );
        
    $this->_reset_write();
    return $this->query($sql);
}
```
I think thats all thats needed to make it work? who knows its a 6am quick code job (NOT TESTED YET)
Example Usage:
``` php
$data = array(
     'id'    => 10,
     'title' => $title,
     'name'  => $name,
     'date'  => $date
     );
$this->db->on_duplicate('mytable', $data);
// INSERT INTO mytable (id, title, name, date)
// VALUES ({$id}, {$title}, {$name}, {$date})
// ON DUPLICATE KEY UPDATE id={$id}, title={$title}, name={$name}, date={$date};
```
For us special ones like me that basically does:
If a primary or unique key in the database is the same as one of the insert values then it updates if not then it inserts.
