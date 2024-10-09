# The "resizeImage:custom" option of the ckeditor5_imageResize plugin does not work.
## Problem/Motivation
Instead of being able to resize images by dragging the mouse along the screen, I need to be able to provide the user with an option to set a specific width for that image (while keeping the aspect ratio). By default, the resize dropdown only provides the 'Original' option to revert it to the original state after manually adjusting the size. By using the hook_ckeditor5_plugin_info_alter() function, I can add specific presets (50px, 100px, 200px, etc.), however, if I add the 'custom' option as mentioned in the CKEditor documentation, nothing happens. The option shows up in the dropdown, I can select it, but the CKEditor input field that is supposed to pop up where you can manually enter a value never shows up. I am also not getting any errors in Watchdog or my console. Since I couldn't find an alternative to setting the height/width of an image, I decided to make this bug post.
## Steps to reproduce
Add the following hook to your custom module. The option will show up, but nothing will happen when you click it.
## Usage/Examples

```javascript
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

