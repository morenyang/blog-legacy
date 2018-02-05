# hexo-theme-simplify

A simple theme for Hexo.
base on [hexo-theme-again](https://github.com/DrakeLeung/hexo-theme-again)

***

**Now you can use your photo as background image for header. And you can set a Dark/Light theme for header or navbar.** 

## Install
1. Execute the following command in your `hexo` directory:
    ```git
    git clone https://github.com/morenyang/hexo-theme-simplify.git themes/simplify
    ```

2. Modify `theme` setting in `_config.yml` to `simplify`.
    ```yml
    theme: simplify
    ```

## Update
1. Backup your `_config.yml` in `themes/simplify` directory;

2. Execute the following command in `themes/simplify` directory:
    ```git
    git pull
    ```

## Custom Styles
You can customize or add styles in [less](http://lesscss.org/) files.
**Before** changing the styles, please:

1. Remove the `style.css` file in `source` directory.

2. Install hexo-renderer-less plugin.
    Execute the following command in your `hexo` directory:
    ```npm
    npm install hexo-renderer-less --save
    ```

3. Clean the `public` directory.
    Execute the following command in your `hexo` directory:
    ```hexo
    hexo clean
    ```

## License
Code released under the MIT license.