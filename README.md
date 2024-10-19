# The "resizeImage:custom" option of the ckeditor5_imageResize plugin does not work.
## Problem/Motivation
Instead of being able to resize images by dragging the mouse along the screen, I need to be able to provide the user with an option to set a specific width for that image (while keeping the aspect ratio). By default, the resize dropdown only provides the 'Original' option to revert it to the original state after manually adjusting the size. By using the hook_ckeditor5_plugin_info_alter() function, I can add specific presets (50px, 100px, 200px, etc.), however, if I add the 'custom' option as mentioned in the CKEditor documentation, nothing happens. The option shows up in the dropdown, I can select it, but the CKEditor input field that is supposed to pop up where you can manually enter a value never shows up. I am also not getting any errors in Watchdog or my console. Since I couldn't find an alternative to setting the height/width of an image, I decided to make this bug post.
## Steps to reproduce
Add the following hook to your custom module. The option will show up, but nothing will happen when you click it.

### Drupal website info:
```php
➜ web$$$ ddev drush st
Drupal version   : 10.3.6                                     
Site URI         : https://d10.ddev.site                      
DB driver        : mysql                                      
DB hostname      : db                                         
DB port          : 3306                                       
DB username      : db                                         
DB name          : db                                         
Database         : Connected                                  
Drupal bootstrap : Successful                                 
Default theme    : uswds                                 
Admin theme      : claro                                      
PHP binary       : /usr/bin/php8.3                            
PHP config       : /etc/php/8.3/cli/php.ini                   
PHP OS           : Linux                                      
PHP version      : 8.3.10                                     
Drush script     : /var/www/html/vendor/bin/drush             
Drush version    : 13.2.0.0                                   
Drush temp       : /tmp                                       
Drush configs    : /var/www/html/vendor/drush/drush/drush.yml 
Install profile  : standard                                   
Drupal root      : /var/www/html/web                          
Site path        : sites/default                              
Files, Public    : sites/default/files                        
Files, Temp      : /tmp
```

## Usage/Examples

```php
/**
* Implements hook_ckeditor5_plugin_info_alter().
*/

function mymodule_ckeditor5_plugin_info_alter(array &$plugin_definitions) {
  $plugin = $plugin_definitions['ckeditor5_imageResize']->toArray();

  $ckeditor5_imageResize['ckeditor5']['config']['image']['resizeOptions'][] = [
    'name' => 'resizeImage:100',
    'label' => '100',
    'value' => '100'
  ];
  $ckeditor5_imageResize['ckeditor5']['config']['image']['resizeOptions'][] = [
    'name' => 'resizeImage:200',
    'label' => '200',
    'value' => '200'
  ];
  $ckeditor5_imageResize['ckeditor5']['config']['image']['resizeOptions'][] = [
    'name' => 'resizeImage:300',
    'label' => '300',
    'value' => '300'
  ];
  $ckeditor5_imageResize['ckeditor5']['config']['image']['resizeOptions'][] = [
    'name' => 'resizeImage:400',
    'label' => '400',
    'value' => '400'
  ];
  $ckeditor5_imageResize['ckeditor5']['config']['image']['resizeOptions'][] = [
    'name' => 'resizeImage:500',
    'label' => '500',
    'value' => '500'
  ];
  $plugin['ckeditor5']['config']['image']['resizeOptions'][] = [
    'name' => 'resizeImage:custom',
    'label' => 'Custom',
    'value' => 'custom',
  ];

  $plugin_definitions['ckeditor5_imageResize'] = new CKEditor5PluginDefinition($plugin);
}
```

# Solution : 

Apply patch: 
1. ```git apply ckeditor5_ckeditor5-dll.js.patch```
2. ```git apply ckeditor5_image.js.patch```

## "file-mapping" OR copy files through composer.js
```php
"extra": {
        "drupal-scaffold": {
            "locations": {
                "web-root": "web/"
            },
            "file-mapping": {
                "[web-root]/core/assets/vendor/ckeditor5/image/image.js": {
                    "mode": "replace",
                    "path": "./patches/image.js",
                    "overwrite": true
                    },
                "[web-root]/core/assets/vendor/ckeditor5/heading/heading.js": {
                    "mode": "replace",
                    "path": "./patches/heading.js",
                    "overwrite": true
                    },
                "[web-root]/core/assets/vendor/ckeditor5/ckeditor5-dll/ckeditor5-dll.js": {
                    "mode": "replace",
                    "path": "./patches/ckeditor5-dll.js",
                    "overwrite": true
                    },
                "[web-root]/core/assets/vendor/ckeditor5/editor-classic/editor-classic.js": {
                    "mode": "replace",
                    "path": "./patches/editor-classic.js",
                    "overwrite": true
                    }
            }
        },
        "installer-paths": {
            "web/core": [
                "type:drupal-core"
            ],
```

## Apply patch
```php
"extra": {
        "enable-patching": true,
        "patches": {
            "drupal/core": {
                "Ckeditor5 image resize custom option - patch-cke_image": "../ckeditor5-image-resize/cke_image.js.patch",
                "Ckeditor5 image resize custom option - patch-cke_heading": "../ckeditor5-image-resize/cke_heading.js.patch",
                "Ckeditor5 image resize custom option - patch-cke_editor-classic": "../ckeditor5-image-resize/cke_editor-classic.js.patch",
                "Ckeditor5 image resize custom option - patch-cke_ckeditor5-dll": "../ckeditor5-image-resize/cke_ckeditor5-dll.js.patch"
            }
        }
}
```
