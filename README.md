# Taxonomy Module

[![Build Status](https://travis-ci.org/wpfulcrum/taxonomy.svg?branch=develop)](https://travis-ci.org/wpfulcrum/taxonomy) 
[![Latest Stable Version](https://poser.pugx.org/wpfulcrum/taxonomy/v/stable)](https://packagist.org/packages/wpfulcrum/taxonomy) 
[![License](https://poser.pugx.org/wpfulcrum/taxonomy/license)](https://packagist.org/packages/wpfulcrum/taxonomy)

The Fulcrum Custom Taxonomy Module makes your job easier for adding custom taxonomies to your project. Pass it a configuration and it handles the rest for you.

## Features

- Registration is handled for you.
- Label generation - handy when you do not need internationalization.
- Stores in Fulcrum's Container - when added, automatically stores it in the Container for global usage.

## Installation

The best way to use this component is through Composer:

```
composer require wpfulcrum/taxonomy
```

## Dependencies

This module requires:
 
- at least PHP 5.6
- WordPress 4.8+

## Configuring a Custom Taxonomy

This module, as with all Fulcrum modules, is configuration driven as part of the ModularConfig design pattern.  In your theme/plugin's configuration folder, you will want to create a configuration file.  Here is the basic structure of that file:

```
<?php

$config = [
    'autoload'     => true,
    'taxonomyName' => 'genre',
    'config'       => [
        'taxonomyConfig' => [],
        'labelsConfig'   => [],
    ],
];

$config['config']['taxonomyConfig'] = [
    'objectType' => ['book'],

    /**
     * Arguments Configuration Parameters
     *
     * @see https://codex.wordpress.org/Function_Reference/register_taxonomy#Arguments for more details.
     *
     * Don't configure the label or labels here.  Those are handled separately below.
     */
    'args'       => [
        'description'       => 'Book Genres',
//        'label'        => '', <- This isn't needed.
//        'labels'       => [], <- don't configure here as they are configured below.
        'hierarchical'      => true,
        'show_ui'           => true,
        'show_admin_column' => true,
        'query_var'         => true,
    ],
];

/**
 * Labels Builder - Configuration Parameters
 */
$config['config']['labelsConfig'] = [

    /***************************************************************************************************
     * When Your Plugin Doesn't Need Internationalization:
     *
     * By default, the label builder automatically builds the labels for you using the plural and singular
     * names you configure below.
     **************************************************************************************************/
    'useBuilder'   => true, // set to false when you need internationalization.
    'pluralName'   => 'Genres',
    'singularName' => 'Genre',

    /***************************************************************************************************
     * Specify the labels you want here.
     *
     * When not using the automatic builder (i.e. when 'useBuilder' is set to `false`), then you specify
     * all the custom labels here.
     *
     * If you are using the builder, any labels you specify here will overwrite what the builder generates.
     *
     * @see https://codex.wordpress.org/Function_Reference/register_taxonomy#Arguments for more details.
     **************************************************************************************************/
    'labels'       => [
        'name'              => _x('Genres', 'taxonomy general name', 'textdomain'),
        'singular_name'     => _x('Genre', 'taxonomy singular name', 'textdomain'),
        'search_items'      => __('Search Genres', 'textdomain'),
        'all_items'         => __('All Genres', 'textdomain'),
        'parent_item'       => __('Parent Genre', 'textdomain'),
        'parent_item_colon' => __('Parent Genre:', 'textdomain'),
        'edit_item'         => __('Edit Genre', 'textdomain'),
        'update_item'       => __('Update Genre', 'textdomain'),
        'add_new_item'      => __('Add New Genre', 'textdomain'),
        'new_item_name'     => __('New Genre Name', 'textdomain'),
        'menu_name'         => __('Genre', 'textdomain'),
    ],
];

return $config;

```

## Making It Work

There are 2 ways to utilize this module:

1. With the full [Fulcrum plugin](https://github.com/wpfulcrum/fulcrum).
2. Or on its own without Fulcrum.

### With Fulcrum

In Fulcrum, your plugin is an Add-on.  In your plugin's configuration file, you will have a parameter for the `serviceProviders`, where you list each of the service providers you want to use.  In this case, you'll use the `provider.taxonomy`.  

For example, using our Book Genre configuration above, this would be the configuration:

```
	'serviceProviders' => [

		/****************************
		 * Custom Post Types
		 ****************************/
		'genre.taxonomy' => array(
			'provider' => 'provider.taxonomy', // this is the service provider to be used.
			'config'   => BOOK_PLUGIN_DIR . 'config/taxonomy/genre.php', // path to the genre's taxonomy configuration file.
		),
	],
```

[Fulcrum's Add-On Module](https://github.com/wpfulcrum/addon) handles flushing the rewrites upon plugin activation and deactivation.  That saves you time.

### Without Fulcrum

Without Fulcrum, you'll need to instantiate each of the dependencies and `Taxonomy`.  For example, you would do:

```
$config = require_once BOOK_PLUGIN_DIR . 'config/taxonomy/genre.php';  // path to the genre's taxonomy configuration file.

$taxonomy = new PostType(
    $config['taxonomyName'],
    ConfigFactory::create($config['config']['taxonomyConfig']),
    new LabelsBuilder(ConfigFactory::create($config['config']['labelsConfig']))
);
$taxonomy->register();    
```

You will need to handle flushing the rewrites with your plugin's activation and deactivation.

## Contributing

All feedback, bug reports, and pull requests are welcome.