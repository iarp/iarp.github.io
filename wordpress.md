# WordPress

Automatic Update Wordpress instance while under VCS control
-----------------------------------------------------------

Add this to the bottom of your wp-config.php file::

```php
function filter_automatic_updates_is_vcs_checkout( $return_false ) { 
    return false; 
};
add_filter( 'automatic_updates_is_vcs_checkout', 'filter_automatic_updates_is_vcs_checkout', 10, 1 ); 
```
