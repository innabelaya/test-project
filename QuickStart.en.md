# Full stack quick start

Within this tutorial we are going to develop an [online shop web page](http://varya.me/online-shop-dummy/desktop.bundles/index/index.html) using BEM principles in CSS-writing, JavaScript and [BEMHTML](http://bem.info/libs/bem-core/2.0.0/bemhtml/rationale/). While developing we will use command-line application: [bem-tools](http://en.bem.info/tools/bem/bem-tools/). 

**Note:** this tutorial requires JavaScript programming language knowledge.

<img height="407" width="624" src="png/2014-02-25_1011.png"> 

All tools that we are going to use work crossplatform.

Be aware that you use suitable versions of bem-tools and libraries to run through this tutorial:  
* [bem-tools v.0.7.x](https://github.com/bem/bem-tools)  
* [bem-core v.2.0.0](https://github.com/bem/bem-core)  

To get started with BEM-based project you need to install the latest version of [Node.js](http://nodejs.org/).

# BEM in short

BEM is an abbreviation of three entities: a **Block**, an **Element** and a **Modifier**. 
 
BEM is a [methodology](http://en.bem.info/method/) of web projects development, that allows you to divide an interface into logically independent blocks. At the same time BEM contains specific tools for the typical web developers' tasks automatization. And finally BEM gives us opportunity to create libraries of web-components for fast and efficient web-development.

# Starting with a new project repository

The quickest and easiest way to start with your own BEM-project is to use an existing template project repository – [project-stub](https://github.com/bem/project-stub). It contains the minimal configuration files and folders you will need for quick start from scratch.  

We need to create a local copy of a `project-stub`. You can choose any of your favorite tools to clone the project. We are going to use Git.

    $ git clone git://github.com/bem/project-stub.git start-pretty-project   
	
Go to a new project directory:

    $ cd start-pretty-project/
Remove the versions history of the origin repository:

    $ rm -rf .git
    
Create a git repository from that directory:
    
    $ git init
    
Install all npm dependances and bem-tools:

    $ npm install  
	
Now you can run any bem-tools commands from a `node_modules/bem/bin/bem` directory.  
To be able to run bem-tools commands without typing a full path to an executable file (`node_modules/bem/bin/bem`), use `bem-cli` npm package:

    $ sudo npm install bem-cli -g
Now you can use bem-tools from any point of your project. 
	 
Build the project:

    $ bem make
The first launch may take some time as required npm packages and dependencies are being installed in background.  
Project's build process configuration is determined in `.bem/make.js` file. It defines all the technologies of blocks implementation (templates, dependencies, CSS rules and JavaScript functionality) that have to be connected to the project's pages by bem-tools. `desktop.bundles/.bem/level.js` configuration file contains a list of libraries to be used in the project.

Starting BEM server for development:

    $ bem server  
Upon completion you will see the following message:
`info: Server is listening on port 8080. Point your browser to http://localhost:8080/`  

This means that [BEM server](https://github.com/bem/project-stub#usage) is up and running. From this point on a solicited part of your project will be rebuilt automatically every time you reload a web page.

**Getting stuck?**  
If port:8080 is already in use by another program, you can redefine it using `-p` option.

		$ bem server -p portNum

# Brief overview of the structure of the project 

HTML layout and CSS rules of each page depend on its BEMJSON description in `pageName.bemjson.js` file called **BEMJSON declaration**. All blocks declared in BEMJSON page file are automatically stored in `desktop.bundles` directory by bem-tools. Every page has its own directory and inside it you can find different files needed for a page to function.   

BEMJSON declaration describes a page structure in BEM terms: blocks, elements and modifiers. **BEMHTML template engine** processes BEMJSON declaration to create HTML layout of a web page.

Blocks are our building materials for each page. You can use the already created blocks from the libraries or create a new one by yourself.

Every block could be implemented in the following technologies: `css`, `js`, `bemhtml`, `deps.js`, `bemjson.js`. We will call them **block's implementation technology files**. 

Blocks' implementation sets are stored in one directory called **redefinition level**.  

[Project structure](http://en.bem.info/method/filesystem/) presumes that all newly created and redefined blocks are stored in a `desktop.blocks` directory. The web pages' blocks and all blocks that are mentioned in BEMJSON declarations are stored in a `desktop.bundles` directory.  

# Step-by-Step

This section provides you with a step-by-step diving into BEM-based development of a web page. Previewing the steps of this tutorial will give you a clear insight into the developing process.

First of all the two main parts of our web page will be defined: a *head* and its main part – *body*.
 
1. We will declare a **head** block in a BEMJSON file of a page and assign the first CSS rules to provide a correct markup within this block. 

2. The **head** block will be extended by a search form and a logo. In the search form we will add **input** and **button** blocks. **Logo** block will be rendered as a link to [bem.info](http://en.bem.info/) site. Using [bem-tools commands](http://en.bem.info/tools/bem/bem-tools/commands/) we will create new blocks and redefine some blocks from a library. 

3. A list of goods will be added to page's body. We will declare it in BEMJSON as a **goods** block and create corresponding BEMHTML template for it. New CSS rules for this block will provide correct layout for a list of goods.

4. For correct appliance of CSS rules and JavaScript implementation of a block we will provide additional dependencies in `deps.js` file.

5. We will link a third-party library to the project and extend JavaScript functionality of the block we are going to use.

6. We will find out how to extend the block functionality using a mix of blocks and elements.  

7. And finally we will show you how to create a new page and start a whole project build procedure.  

# Changing Pages

You have just one page in your project to begin with: `index.html`.  
Try and open it in your browser: `http://localhost:8080/desktop.bundles/index/index.html`. 

**Note!**  
Make sure that you specify the full path to the `index.html` page: `http://localhost:8080/desktop.bundles/index/index.html`, otherwise some problems with relative paths could occur and all CSS rules will be ignored. 

## Declaring a block in BEMJSON

Let's add a **head** block to the page. To do this declare it in a BEMJSON file of a `/desktop.bundles/index/index.bemjson.js` page.

    { block: 'head' }

From this point on find code snapshots on [Gist](https://gist.github.com/innabelaya/8885713).  

Refresh the page to see the corresponding `<div>` with a `"head"` class:
   
    <!DOCTYPE html>
    <html class="ua_js_no">
        <head>...</head>
        <body class="page">
            <div class="head"></div>
            <script src="_index.js"></script>
        </body>
    </html>
    
**Head** block consists of a search form, a logo and a layout block. The last one provides correct markup within the  **head**.

First of all put a **layout** block along with its two elements (**left** and **right**) inside the **head** block.

    content: [
        {
            block: 'head',
            content: {
                block: 'layout',
                content: [
                    {
                        elem: 'left',
                        content: 'left here'
                    },
                    {
                        elem: 'right',
                        content: 'right here'
                    }
                ]
            }
        }
    ]
[Code sample](https://gist.github.com/innabelaya/8885938) index.bemjson.js. 

Refresh the page to view the new corresponding `<div>`'s:

    <!DOCTYPE html>
    <html class="ua_js_yes">
        <head>...</head>
        <body class="page">
            <div class="head">
                <div class="layout">
                    <div class="layout__left">left here</div>
                    <div class="layout__right">right here</div>
                </div>
            </div>
            <script src="_index.js"></script>
        </body>
    </html>
    
This markup requires CSS rules to be added. In BEM terms you have to implement a **layout** block in CSS.

## Creating a new block

To implement a block using CSS technology you have to create a CSS file for this block in a corresponding block directory. For this use `bem create` command of [bem-tools](http://en.bem.info/tools/bem/bem-tools/commands/):

    $ bem create -l desktop.blocks -b layout -T css
where:  
`-l directoryName` – defines a redefinition level;  
`-b blockName` – defines a name of the block's directory for which a technology file will be created.  
`-T technogyName` – creates technology file for block's implementation.   

Running this command creates `desktop.blocks/layout/layout.css` file. Inside you will find a CSS selector that matches the **layout** block. It is where you step in and fill the selector up with CSS properties. 
Or just copy and paste from [Gist](https://gist.github.com/innabelaya/8906070).
 
You can create the blocks manually. To do so just create `desktop.blocks/layout/` directory and put there all required block's implementation technology files.

**Logo** block will consist of an icon with a slogan. To insert it into the **head** block we have to declare a **logo** block in an `index.bemjson.js` file and add CSS rules for it.

You can use our [cute BEM image](http://varya.me/online-shop-dummy/desktop.blocks/b-logo/b-logo.png) for the logo or pick any other image you like :-) 

    {
        elem: 'right',
        content: {
            block: 'logo',
            content: [
                {
                    block: 'icon',
                    tag: 'img',
                    attrs: { src: 'http://varya.me/online-shop-dummy/desktop.blocks/b-logo/b-logo.png' }
                },
                {
                    elem: 'slogan',
                    content: 'A new way of thinking'
                }
            ]
        }
    }
[Code sample](https://gist.github.com/innabelaya/9345355) index.bemjson.js. 

![Блок logo](png/2014-02-25_1137.png)

## Using blocks of a library  

You do not need to implement an **input** and a **button** blocks yourself. They are provided by the [bem-components library](https://github.com/bem/bem-components) which is linked to the project-stub by default. So you can just declare these blocks in `desktop.bundles/index/index.bemjson.js` file.

    {
        elem: 'left',
        content: [
            {
                block: 'input',
                name: 'text',
                val: 'Find'
            },
            {
                block: 'button',
                type: 'submit',
                content: 'Search'
            }
        ]
    }

[Code sample](https://gist.github.com/innabelaya/8912696) index.bemjson.js.

Let's add Yandex.Browser search results to the search form.

```
{
    elem: 'left',
    content: {
        tag: 'form',
        attrs: { action: 'http://yandex.ru/yandsearch' },
        content: [
            {
                block: 'input',
                name: 'text',
                val: 'Find'
            },
            {
                block: 'button',
                type: 'submit',
                content: 'Search'
            }
        ]
    }
}
``` 
[Code sample](https://gist.github.com/innabelaya/9391751) index.bemjson.js.

![Search form](png/2014-02-25_1140.png)

Use a **link** block from the same library to render an **icon** block as a link to [bem.info](http://en.bem.info/) site. 

```
{
    elem: 'right',
    content: {                                   
        block: 'logo',
        content: [
            {
                block: 'link',
                url: 'http://ru.bem.info',
                content: [
                    {
                        block: 'icon',
                        url: 'http://varya.me/online-shop-dummy/desktop.blocks/b-logo/b-logo.png'
                    }
                ]
            },
            {
                elem: 'slogan',
                content: 'A new way of thinking'
            }
        ] 
    }    
}
```
[Code sample](https://gist.github.com/innabelaya/9345693) index.bemjson.js.

## Modifying the library blocks

### Modifying a block in CSS

The blocks **input** and **button** can be modified using additional CSS rules.

The CSS files for each block have to be stored in a `desktop.blocks` redefinition level:

    $ bem create -l desktop.blocks -b input -T css 
[Code sample](https://gist.github.com/innabelaya/8906605) input.css.

Run the same command for a **button** block:

    $ bem create -l desktop.blocks -b button -T css 
[Code sample](https://gist.github.com/innabelaya/8906646) button.css.

The same can be done for a **link** block:

    $ bem create -l desktop.blocks -b link -T css 
[Code sample](https://gist.github.com/innabelaya/8906451) link.css.

![Search form](png/2014-02-25_1145.png)

### Modifying BEMHTML

You need an additional HTML element – a container – to center the page. It is not necessary that you need to create an additional block. The more correct way rather be to modify a **page** block's template at a `desktop.blocks` redefinition level. This template will generate an output HTML for the entire page. 

We are going to use [BEMHTML](http://bem.info/libs/bem-core/2.0.0/bemhtml/reference/) as a template language.

    $ bem create -l desktop.blocks -b page -T bemhtml
You can use BEMHTML templates not only to declare HTML tags to output but also to generate additional markup depending on view.

Add some code to wrap the page contents in additional container node; put it into a newly created `desktop.blocks/page/page.bemhtml` file.

    block('page').elem('body').match(!this._done)(
        content()(function() {
            this._done = true;
            return {
                elem: 'inner',
                content: applyNext()
            };
        })
    )

[Code sample](https://gist.github.com/innabelaya/8906664)  page.bemhtml.

    <!DOCTYPE html>
    <html class="ua_js_yes">
        <head>...</head>
        <body class="page">
            <div class="page__inner">
                <div class="head">
                    <div class="layout">...</div>
                </div>
                <script src="_index.js"></script>
            </div>
        </body>
    </html>

Then implement the **page** block in CSS technology to apply style to resulting markup:

    $ bem create -l desktop.blocks -b page -T css 
Copy CSS code for a newborn `desktop.blocks/page/page.css` file from [here](https://gist.github.com/innabelaya/8906698).

Define a border property for the **head** block to make it visible on a page.

    $ bem create -l desktop.blocks -b head -T css 
Once again, you can borrow contents for a `desktop.blocks/head/head.css` file from [here](https://gist.github.com/innabelaya/8906724).

![Head block with a frame](png/2014-02-25_1149.png)

# BEMHTML templates

A web page we are going to develop contains a list of some goods. To be able to add it to the page you have to declare a **goods** block in `desktop.bundles/index/index.bemjson.js` file. There are the following goods' data available: title, image, price and link for each item.

    {
        block: 'goods',
        goods: [
            {
                title: 'Apple iPhone 4S 32Gb',
                image: 'http://mdata.yandex.net/i?path=b1004232748_img_id8368283111385023010.jpg',
                price: '259',
                url: '/'
            },
            {
                title: 'Samsung Galaxy Ace S5830',
                image: 'http://mdata.yandex.net/i?path=b0206005907_img_id5777488190397681906.jpg',
                price: '73',
                url: '/'
            },
            //...
    }
[Code sample](https://gist.github.com/innabelaya/8913801) index.bemjson.js.

This block has to be implemented in BEMHTML technology in order to be turned into an appropriate piece of HTML. It needs to be styled with CSS as well. So you can create this block with all the default technologies just by removing `-T` flag.

    $ bem create -l desktop.blocks -b goods
Then write BEMHTML code in `desktop.blocks/goods/goods.bemhtml` file that processes BEMJSON input data into the block's elements. Use a `tag` mode to define an HTML representation of a **goods** block and its elements as well.

    block('goods')(
		tag()('ul'),
		
		//...

	    	elem('item')(
				tag()('li')
    		),

			elem('title')(
        	tag()('h3')
    		),

    		elem('image')(
        	tag()('img'),

        	attrs()({ src: this.ctx.url })   
    		),

			elem('price')(
        	tag()('span')
        	)
[Code sample](https://gist.github.com/innabelaya/8913843) goods.bemhtml.

    <!DOCTYPE html>
    <html class="ua_js_yes">
        <head>...</head>
        <body class="page">
            <div class="page__inner">
                <div class="head">...</div>
                <ul class="goods">
                    <li class="goods__item">
                        <h3 class="goods__title">Apple iPhone 4S 32Gb</h3>
                        <img class="goods__image" src="http://mdata.yandex.net/i?path=b1004232748_img_id8368283111385023010.jpg"/>
                        <span class="goods__price">259</span>
                    </li>
                    <li class="goods__item">...</li>
                    <li class="goods__item">...</li>
                </ul>
                <script src="_index.js"></script>
            </div>
        </body>
    </html>
Templates can produce not only HTML elements of a block but nested blocks as well. The example below shows you how to render a price element as a **link**.

An extra trick: if you would like to avoid cascade when styling the block, mark this link as an element of **goods** block.

    {
        elem: 'price',
        content: {
            block: 'link',
            mix: [ {block: 'goods', elem: 'link'} ],
            url: item.url,
            content: item.price
        }
    }

[Code sample](https://gist.github.com/innabelaya/8913983) goods.bemhtml.


    <!DOCTYPE html>
    <ul class="goods">
        <li class="goods__item">
            <h3 class="goods__title">
                Apple iPhone 4S 32Gb
            </h3>
            <img class="goods__image" src="http://mdata.yandex.net/i?path=b1004232748_img_id8368283111385023010.jpg"/>
            <a class="link goods__link" href="/">259</a>
        </li>
        //...
        <li class="goods__item">...</li> 
        <li class="goods__item">...</li>
    </ul> 

You need to identify new goods on a page. To implement this add a verification of a `new` modifier to the template: [code sample](https://gist.github.com/innabelaya/8914048). 

Use this code snapshot for [CSS rules](https://gist.github.com/innabelaya/8915049).  
Notice that you do not need to create CSS file for this block because it had already been generated using `bem create` command, and CSS is one of the block's default technologies.

![List of goods](png/2014-02-25_1203.png) 

You also need some extra CSS for an IE browser since it is not among the list of default block technologies.

    $ bem create -l desktop.blocks -b goods -T ie.css
Again, content for the resulting `desktop.blocks/goods/goods.ie.css` file is available on [Gist](https://gist.github.com/innabelaya/8915092).

# Blocks' dependencies

Besides declaring blocks within input BEMJSON data you need to make sure that the corresponding templates, CSS and JavaScript are linked to the page. You can do this using a special `deps.js` block technology which is used to describe block dependencies.

    $ bem create -l desktop.blocks -b goods -T deps.js 
You can use moderate dependency type codenamed `shouldDeps` and declare that you need a **link** block.

    ({
        shouldDeps: [
            { block: 'link' }
        ]
    })
[Code sample](https://gist.github.com/innabelaya/8915140) goods.deps.js.

# Using a third-party libraries

It would be nice to have each item in the list of goods rendered as a rectangle with a shadow. We can borrow a block from a third-party block library called `j`.
It provides just one block **box** that does all we need.

You should declare a library name and its version (if available) in a `.bem/make.js` file.

```
libraries: [
        'bem-core @ v2.0.0',
        'bem-components @ 79ca4740c605339941e2a560c6681bfea02f00b3',
        'j'
    ]
```    
[Code sample](https://gist.github.com/innabelaya/8915341) .bem/make.js.

Declare a library repository URL in a `.bem/repo.db.js` file to get its code into your project.

    module.exports = {

    'bem-components' : {
        type     : 'git',
        url      : 'git://github.com/bem/bem-components.git'
    },
    'bem-core' : {
        type     : 'git',
        url      : 'git://github.com/bem/bem-core.git'
    },
      
        //...
       
        'j': {
        type     : 'git',
        url      : 'git://github.com/innabelaya/j.git'
    }

    };
[Code sample](https://gist.github.com/innabelaya/8915389) .bem/repo.db.js.

Next make your pages take blocks from the block level provided by the library. Do this by tuning a bundle configuration in a `desktop.bundles/.bem/level.js`.

    exports.getConfig = function() {

    return BEM.util.extend(this.__base() || {}, {
        bundleBuildLevels: this.resolvePaths([
                'bem-core/common.blocks',
                'bem-core/desktop.blocks',
                'bem-components/common.blocks',
                'bem-components/desktop.blocks',
                'j/blocks'
            ]
[Code sample](https://gist.github.com/innabelaya/8915431) desktop.bundles/.bem/level.js.

You need to restart BEM server after changing the configuration to apply all changes. Kill the current process (`Ctrl+C`) and run `bem server` again.

# Mix of blocks and elements

Having linked the library you can use a **box** block. To add a white background with a shadow to the **head** block you need to mix it with the **box** block.

One of the possible ways to mix blocks is to declare this mix in BEMJSON input data.  

Here you can mix **head** and **box** blocks:

    {
        block: 'head',
        mix: [ { block: 'box' } ],
        content: ...
    }
[Code sample](https://gist.github.com/innabelaya/8931642) index.bemjson.js.

    <!DOCTYPE html>
    <html class="ua_js_yes">
        <head>...</head>
        <body class="page">
            <div class="page__inner">
                <div class="head box">
                    <div class="layout">...</div>
                </div>
                <ul class="goods">...</ul>
                <script src="_index.js"></script>
            </div>
        </body>
    </html>
Do not forget to define that **head** block requires **box** block.

    $ bem create -l desktop.blocks -b head -T deps.js 

```
({
    shouldDeps: [
        { block: 'box' }
    ]
})
```
[Code sample](https://gist.github.com/innabelaya/8930709) head.deps.js.

<img height="157" width="624" src="png/2014-02-25_1216.png">

You can also mix an element with a block using BEMHTML templates of a block.  
Let's specify that each item element from **goods** block has the same formatting as **head** block. For this you need to mix each **item** from **goods** block with **box** block from `j` library.

```
    elem: 'item',
        mods: { new: item.new ? 'yes' : undefined },
        mix: [{ block: 'box' }],
        content: ...
```
        
[Code sample](https://gist.github.com/innabelaya/8930835) goods.bemhtml.

    <!DOCTYPE html>
    <html class="i-ua_js_yes">
        <head>...</head>
        <body class="page">
            <div class="page__inner">
                <div class="head box">...</div>
                <ul class="goods">
                    <li class="goods__item box">...</li>
                    <li class="goods__item box">...</li>
                    <li class="goods__item box">...</li>
                    <li class="goods__item goods__item_new_yes box">...</li>
                    <li class="goods__item box">...</li>
                    
                    //...
                    
                </ul>
                <script src="_index.js"></script>
            </div>
        </body>
    </html>

![Список товаров в блоке box](png/2014-02-25_1221.png)

# Declarative JavaScript

## JavaScript for a block

**Box** block borrowed from a third-party library supports roll up animation implemented in JavaScript.

To use this functionality in **head** block you need to change the block BEMJSON declaration and set JavaScript property to `true` for mixed **box** block.

    mix: [{ block: 'box', js: true }]
[Code sample](https://gist.github.com/innabelaya/8930981) index.bemjson.js.

It is required to have `switcher` element in the block.

    block: 'head',
    mix: [ { block: 'box', js: true } ],
    content: [
        {
            block: 'layout',
            
        //...
        
        },
        {
            block: 'box',
            elem: 'switcher'
        }
    ]
[Code sample](https://gist.github.com/innabelaya/8931082) index.bemjson.js.

With that you have a block with a clickable arrow-shaped element which rolls the block up.

![Arrow](png/2014-02-25_1227.png)

## Modifying JavaScript

What if you are not satisfied with the dynamic functionality provided by the **box** block? Maybe you would like it to roll up and left. Usually you cannot alter the code of the library you borrowed your block from as it is not yours.
But thanks to using the [i-bem.js](https://github.com/bem/bem-core/blob/v1/common.docs/i-bem-js/i-bem-js.en.md) declarative framework you can change JavaScript implementation of a block.

    $ bem create -l desktop.blocks -b box -T js 
You need to use the `onSetMod` property to specify a block reaction depending on modifier's state change.

In this example the block is told to respond to setting up and removing a `closed` modifier.

    onSetMod : {

        'closed': {
            'yes': function() {
                // some functionality here
            },
            '': function() {
                // some functionality here
            }
        }
        
    }
[Code sample](https://gist.github.com/innabelaya/9503213) box.js.

# Creating a new page

In a BEM world a page is also a block but at the `desktop.bundles` redefinition level. So you can use `bem create` command for pages as well. Create a new page of the project called **contact**:

    $ bem create -l desktop.bundles -b contact

As you can see there is no `-T` flag here. That is because the `desktop.bundles` block level implementation defaults to BEMJSON technology.
As a result in `desktop.bundles/contact/contact.bemjson.js` file being filled with dummy page contents.

Load a new page in a browser: `http://localhost:8080/desktop.bundles/contact/contact.html`.
BEM server builds it for us upon the first access.

# Starting building the project

While developing every time you reload a page in a browser a `bem server` rebuilds what has to be rebuilt following your changes.

To rebuild the entire project you can use a `bem make` command:

    $ bem make

# Key take-aways

This tutorial just lets us to open a door to the BEM world. The entire information about BEM methodology is available at the [bem.info](http://bem.info/) site. You can find there a list of overview articles, tutorials, references, workshops and video presentations.

# Releases

This tutorial is based on the [Full stack quick start](http://bem.info/articles/start-with-project-stub/) publication by Varvara Stepanova.

The current release includes:
* bem-core v2.0.0;
* bem-tools v0.7.x;
* updated bem-components library;
* new JavaScript syntax of BEMHTML.
