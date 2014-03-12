#Попробуй БЭМ на вкус!
Эта статья рассказывает о том, как быстро создать свой проект с использованием БЭМ-технологий.  
Для изучения материала, представленного в статье, необходимо знание языка JavaScript.

Мы шаг за шагом создадим [страничку каталога товаров](http://varya.me/online-shop-dummy/desktop.bundles/index/index.html), пользуясь принципами БЭМ в CSS, возможностью писать декларативный JavaScript с использованием фреймворка [i-bem.js](http://ru.bem.info/articles/bem-js-main-terms/) и шаблонизатора [BEMHTML](http://ru.bem.info/libs/bem-core/1.1.0/rationale/). Помогать делать это будут инструменты для работы с файлами по БЭМ-методолгии – [bem-tools](http://ru.bem.info/tools/bem/bem-tools/). 

<img height="407" width="624" src="https://jing.yandex-team.ru/storage/neige/920060/2014-02-25_1011.png"> 

Инструменты, которые мы собираемся использовать, являются кроссплатформенными.  

Обратите внимание на актуальность версий инструментов и библиотек:  
* [bem-tools v.0.7.x](https://github.com/bem/bem-tools)  
* [bem-core v.1.1.0](https://github.com/bem/bem-core)  

Для начала работы с любым БЭМ-проектом вам необходимо установить [Node.js](http://nodejs.org/).

#Что такое БЭМ?

Небольшое лирическое отступление для тех, кто не в курсе, что обозначает эта аббревиатура.  
БЭМ расшифровывается как «Блок, Элемент, Модификатор». Это [методология](http://ru.bem.info/method/) разработки web-проектов, способ удобно делить интерфейс на отдельные независимые части, применимый для любой технологии. Кроме того, БЭМ — это набор инструментов для автоматизации работы типичных задач web-разработчика. И наконец, БЭМ — это возможность создания библиотек web-компонентов для быстрой и эффективной разработки.

#Создание собственного репозитория проекта

Самый оптимальный путь создания собственного проекта – использование шаблонного репозитория [project-stub](https://github.com/bem/project-stub). Он содержит необходимый минимум конфигурационных файлов и папок для быстрого развертывания проекта. 

Нам нужна локальная копия `project-stub`. Ее можно сделать любым удобным для вас способом; мы собираемся использовать Git.

	$ git clone git://github.com/bem/project-stub.git start-pretty-project   
	
Переходим в папку нашего проекта:

	$ cd start-pretty-project/
Удаляем всю историю версионирования исходного репозитория:

	$ rm -rf .git
    
Инициализируем собственный репозиторй в папке проекта:
    
	$ git init
    
Выполняем установку всех зависимостей и устанавливаем bem-tools:

	$ npm install  
	
Теперь вызов всех команд bem-tools возможен из папки `node_modules/bem/bin/bem`. Согласитесь, путь длинноват и это не всегда удобно. Упростим запуск, установив npm-пакет `bem-cli`, который позволит нам использовать bem-tools из любой точки проекта.  

	$ sudo npm install bem-cli	
	 
Проект нужно собрать:

    $ bem make
Сборка займет некоторое время, потому что именно в этот момент в директорию проекта устанавливаются все необходимые npm-пакеты. 
При сборке проекта в него подключаются все библиотеки блоков, указанные в конфигурационном файле `desktop.bundles/.bem/level.js`, и все блоки, участвующие в описании страниц. Конфигурация процесса сборки указана в файле `.bem/make.js`. На ее основе bem-tools подключает все технологии, которые составляют реализацию блоков: шаблоны, зависимости, CSS-правила и JavaScript-функциональность.

Для web-разработки запускаем команду:

    $ make server  
В результате вы увидите следующее сообщение:

info: Server is listening on port 8080. Point your browser to http://localhost:8080/

На вашем компьютере запустился [bem server](https://github.com/bem/project-stub#usage) — инструмент для разработки, который при обновлении страницы в браузере будет автоматичекси пересобирать только ту часть проекта, которую затронули ваши изменения.

**Проблема?**  
Если порт 8080 уже используется другой программой, его можно переназначить с помощью опции `-p`:

	$ bem server -p portNum

#Вкратце о структуре проекта

HTML-разметка web-страницы и применяемые к ней CSS-правила генерируются из ее описания в BEMJSON-файле `pageName.bemjson.js`. В терминах БЭМ-методологии будем называть BEMJSON-описание сираницы **декларацией**.  

BEMJSON-декларация представляет собой структуру страницы, описанную в терминах блоков, элементов и модификаторов. Для создания HTML-представления web-страницы в работу включается **шаблонизатор BEMHTML**, который преобразует входные данные из BEMJSON-файла в HTML.

Блоки мы можем заимствовать из сторонних библиотек или создавать самостоятельно.   

Данные блока могут состоять из файлов `css`, `js`, `bemhtml`, `deps.js`, `bemjson.js`, `bemdecl.js`, которые в БЭМ-методологии называются **файлами технологий реализации блока**. Наборы реализаций блоков хранятся в одной директории. В БЭМ-терминах она называется **уровнем переопределения**. 

[Структура проекта](http://ru.bem.info/method/filesystem/) предполагает, что все созданные и переопределенные блоки размещаются в директории `desktop.blocks`. А директория `desktop.bundles` содержит блоки страниц проекта и все блоки, указанные в их BEMJSON-декларациях. Именно эти блоки и участвуют в сборке страницы.  

#Шаг за шагом
В этом разделе кратко описывается последовательность действий, которые мы будем совершать, чтобы создать страницу каталога товаров. Для удобства определим, что страница будет состоять из шапки и тела - основной части.
 
1. Разместим на странице шапку. В терминах БЭМ-методолгии она будет представлена блоком **head**. Для этого задекларируем его в BEMJSON-файле и создадим первые CSS-правила, обеспечивающие разметку.  

2. Добавим в шапку форму поиска и логотип. Представим логотип блоком **logo** и сделаем картинку ссылкой на сайт [bem.info](http://ru.bem.info/). Используя [команды bem-tools](http://ru.bem.info/tools/bem/bem-tools/commands/) создадим блоки самостоятельно и переопределим существующие блоки библитек с помощью технологий CSS и BEMHTML.  

3. В теле страницы разместим список товаров. Представим его блоком **goods** в BEMJSON-декларации. В BEMHTML-шаблоне зададим разметку элементам блока и откорректируем внешний вид CSS-правилами.  

4. Укажем зависимости в файле `deps.js`, чтобы шаблоны, JavaScript-реализация и CSS-правила применились при сборке к нужным нам блокам.

5. Подключим сторонние библиотеки в проект и расширим их функциональность с помощью микса блоков и доопределения JavaScript-функциональности.  

6. Рассмотрим варианты микса блоков и элементов.

7. И, напоследок, создадим новую страницу и запустим полную сборку проекта.  

#Внесение изменения в страницы

Сейчас в проекте есть одна страница index.html, которую можно открыть в браузере: `http://localhost:8080/desktop.bundles/index/index.html`.

Убедитесь, что путь к странице указан полностью. В противном случае, некоторые CSS-правила могут игнорироваться в процессе сборки.

##Описание блока в BEMJSON

Для начала разместим на странице Шапку, добавив декларацию блока **head** в BEMJSON-файл страницы.

    { block: 'head' }
Здесь и далее полный код страницы на разных стадиях доступен на [Gist](https://gist.github.com/innabelaya/8885713).  

Перезагрузив страницу, вы увидите, что в ее HTML-представлении появился соответствующий `<div>` с классом `"head"`.

    <!DOCTYPE html>
    <html class="ua_js_yes ua_css_standard">
        <head>...</head>
        <body class="page page__body">
            <div class="head"></div>
        </body>
    </html>
    
В шапку мы поместим форму поиска, логотип и раскладку, располагающую содержание как нужно.

Сначала в BEMJSON-описании страницы внутрь блока **head** поместим блок **layout** с двумя элементами: **left** и **right**.

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
[Пример кода](https://gist.github.com/innabelaya/8885938) для index.bemjson.js. 

В HTML-представлении страницы появится необходимая разметка (вы сможете увидеть ее, обновив страницу).  

    <!DOCTYPE html>
    <html class="ua_js_yes ua_css_standard">
        <head>...</head>
        <body class="page page__body">
            <div class="head">
                <div class="layout">
                    <div class="layout__left">left here</div>
                    <div class="layout__right">right here</div>
                </div>
            </div>
        </body>
    </html>
    
Теперь для блока **layout** необходимо прописать CSS-правила. В БЭМ-терминах будем называть это реализацией блока в технологии CSS.

##Создание блока

Для создания директории блока и в нем CSS-файла технологии воспользуемся [командой bem-tools](http://ru.bem.info/tools/bem/bem-tools/commands/) `bem create`.

    $ bem create -l desktop.blocks/ -b layout -T css
где:  
`-l directoryName` – указывает на уровень переопределения;  
`-b blockName` – определяет имя директории блока, для которого создается файл технологии. Если директории с таким именем еще не существует, создает ее;  
`-T technogyName` – создает указанный файл технологии реализации блока.   

Таким образом, команда создаст директорию для блока **layout** на уровне переопределения `desktop.blocks` и файл `desktop.blocks/layout/layout.css` для него, в котором уже есть селектор, совпадающий с именем блока. 

Правило нужно дополнить соответственно назначению блока.
Сейчас можно просто скопировать [пример](https://gist.github.com/innabelaya/8906070).

Блоки можно создавать и вручную: создадим папку `desktop.blocks/layout/` и в ней разместим необходимые нам файлы технологий реализации блока. 

Мы хотим, чтобы блок **logo** состоял из картинки и слогана. Для этого задекларируем его в блоке **head** файла `desktop.bundles/index/index.bemjson.js`.

Картинку для логотипа можно взять [отсюда](http://varya.me/online-shop-dummy/desktop.blocks/b-logo/b-logo.png) или указать свою.

```
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
```
[Пример кода](https://gist.github.com/innabelaya/8912696) index.bemjson.js. 

![Блок logo](https://jing.yandex-team.ru/storage/neige/567104/2014-02-25_1137.png)

Добавим необходимые CSS-правила для блока **logo**.

```
    $ bem create -l desktop.blocks/ -T css -b logo
```
[Пример кода](https://gist.github.com/innabelaya/8906451) logo.css.

![Блок logo](https://jing.yandex-team.ru/storage/neige/699295/2014-02-25_1139.png)

##Использование блоков из библиотеки

Блоки поисковой формы **input** и **button** создавать самостоятельно не нужно. Они уже реализованы в библиотеке [bem-components](https://github.com/bem/bem-components), которая подключается в project-stub по умолчанию. Достаточно просто задекларировать блоки на странице `desktop.bundles/index/index.bemjson.js`.  

```
{
	elem: 'left',
	content: [
		{
			block: 'input'
		},
		{
			block: 'button',
			content: 'Search'
		}
	]
}
```
[Пример кода](https://gist.github.com/innabelaya/8912696) index.bemjson.js.

![Форма поиска](https://jing.yandex-team.ru/storage/neige/666549/2014-02-25_1140.png)

Используя блок **link** из той же библиотеки, мы сделаем весь блок **icon** ссылкой на сайт [bem.info](http://ru.bem.info/):


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
[Пример кода](https://gist.github.com/innabelaya/8912696) index.bemjson.js.

##Модификация блоков библиотек

###Модификация в CSS

Блоки **input** и **button** можно модифицировать, написав необходимые CSS-правила для каждого из них.

CSS-разметку мы поместим в блок **input** на уровне переопределения `desktop.blocks`:

    $ bem create -l desktop.blocks/ -b input -T css 
[Пример кода](https://gist.github.com/innabelaya/8906605) input.css.

То же самое для блока **button**:

    $ bem create -l desktop.blocks/ -b button -T css 
[Пример кода](https://gist.github.com/innabelaya/8906646) button.css.

![Форма поиска](https://jing.yandex-team.ru/storage/neige/673342/2014-02-25_1145.png)

###Модификация BEMHTML

Чтобы отцентрировать содержание страницы, нужно создать дополнительный HTML-элемент — контейнер. Для этого необязательно создавать специальный блок, проще и правильнее 
модифицировать шаблон для блока **page** на уровне переопределения `desktop.blocks`, который генерирует выходной HTML для всей страницы. 

В качестве шаблонизатора используем [BEMHTML](http://ru.bem.info/libs/bem-core/1.1.0/reference/).

    $ bem create -l desktop.blocks/ -b page -T bemhtml
В созданном файле `desktop.blocks/page/page.bemhtml` необходимо написать код, оборачивающий контент блока в дополнительный контейнер.  

```
   block('page')(
	content()(
		{
			elem: 'body-i',
			content: applyNext()
		}
	)
)
```
[Пример кода ](https://gist.github.com/innabelaya/8906664)  page.bemhtml.

    <!DOCTYPE html>
    <html class="ua_js_yes ua_css_standard">
        <head>...</head>
        <body class="page page__body">
            <div class="page__body-i">
                <div class="head">
                    <div class="layout">...</div>
                </div>
                <script src="_index.js"></script>
            </div>
        </body>
    </html>

Для новой разметки блока **page** создадим свои CSS-правила:

    $ bem create -l desktop.blocks/ -b page -T css 
Контент для файла `desktop.blocks/page/page.css` можно скопировать [отсюда](https://gist.github.com/innabelaya/8906698).

Чтобы шапка была заметна на странице, поместим ее в рамку. Для этого создадим CSS-правила для блока **head**.

    $ bem create -l desktop.blocks/ -b head -T css 
Контент для файла `desktop.blocks/head/head.css` можно скопировать [отсюда](https://gist.github.com/innabelaya/8906724).

![Блок head с рамкой](https://jing.yandex-team.ru/storage/neige/844267/2014-02-25_1149.png)

#BEMHTML-шаблоны

BEMHTML-шаблоны могут не просто определять теги, которыми представлен блок, и их атрибуты, но и генерировать разметку страницы.

Разместим на странице список товаров. Он представлен в BEMJSON-декларации страницы блоком **goods**. Декларация содержит данные о товарах: название, картинку, цену и адрес.

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
            ...
    }
[Пример кода](https://gist.github.com/innabelaya/8913801) index.bemjson.js.

Чтобы эти данные превратились в нужную разметку, блок должен быть реализован в технологии BEMHTML. Для корректировки внешнего вида применим CSS-правила. Воспользуемся командой `bem create`, чтобы создать блок сразу во всех технологиях, предусмотренных по умолчанию.

    $ bem create -l desktop.blocks -b goods
В BEMHTML-шаблоне блока `desktop.blocks/goods/goods.bemhtml` нужно написать код, который превратит данные, задекларированные в BEMJSON, в элементы блока. А также, пользуясь модой `tag`, указать, как будет представлен блок и его элементы в HTML-структуре страницы.

    block('goods')(
		tag()('ul'),
		
		...

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
[Код пример](https://gist.github.com/innabelaya/8913843) goods.bemhtml.

    <!DOCTYPE html>
    <html class="ua_js_yes ua_css_standard">
        <head>...</head>
        <body class="page page__body">
            <div class="page__body-i">
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
            </div>
        </body>
    </html>
Шаблон может создавать не только HTML-элементы блока, но и другие блоки. Например, цену товара можно сделать ссылкой, используя для этого блок **link** из библиотеки bem-components.

```
{
	elem: 'price',
		block: 'link',
		url: item.url,
		content: item.price
}
```
[Пример кода](https://gist.github.com/innabelaya/8913983) goods.bemhtml.

Чтобы избежать каскада при оформлении этой ссылки стилями, пометим ее как элемент блока **goods**.

```
{    
	elem: 'price',
        block: 'link',
        mix: [{ block: 'goods', elem: 'link' }],
        url: item.url,
        content: item.price
}
```
[Пример кода](https://gist.github.com/innabelaya/8914048) goods.bemhtml.

```
<!DOCTYPE html>
         <ul class="goods">
            <li class="goods__item">
               <h3 class="goods__title">Apple iPhone 4S 32Gb</h3>
               <img class="goods__image" src="http://mdata.yandex.net/i?path=b1004232748_img_id8368283111385023010.jpg"/>
               <div class="link__price goods__link">259</div>
            </li>
            <li class="goods__item">...</li>
            <li class="goods__item">...</li>
```  
Также нужно пометить элементы, которые информируют о новизне товара, модификатором и добавить выравнивающих элементов: .

Нужно визуально выделить на странице новые товары. Для этого добавим проверку модификатора `new` в шаблон: [пример](https://gist.github.com/innabelaya/8914048). 

CSS для блока можно скопировать [отсюда](https://gist.github.com/innabelaya/8915049).  
Создавать блок отдельно в технологии CSS не нужно, потому что CSS входит в список технологий, создаваемых по умолчанию командой `bem create`.

![Список товаров](https://jing.yandex-team.ru/storage/neige/699690/2014-02-25_1203.png) 

Так как мы планируем поддерживать Internet Explorer, необходимо создать специальный `ie.css`-файл. Он не входит в список технологий по умолчанию.

    $ bem create -l desktop.blocks/ -T ie.css -b goods
Код файла `desktop.blocks/goods/goods.ie.css` доступен на [Gist](https://gist.github.com/innabelaya/8915092).

#Зависимости блоков

Помимо декларации нужно гарантировать подключение к странице шаблонов, CSS и JavaScript-блока. Для этого необходимо указать зависимости, это делается представлением блока в технологии `deps.js`.

    $ bem create -l desktop.blocks/ -b goods -T deps.js 
Воспользуемся нестрогой зависимостью `shouldDeps`, указав блок **link**.

    ({
        shouldDeps: [
            { block: 'link' }
        ]
    })
[Пример кода](https://gist.github.com/innabelaya/8915140) goods.deps.js.

#Подключение библиотек

Представим шапку и каждый товар модными прямоугольниками с тенью. Блок для этого мы позаимствуем из сторонней библотеки.
Там есть всего один блок, который называется **box** и делает то, что нам нужно.

Чтобы получить код библиотеки, нужно указать ее имя в файле `.bem/make.js`.

```
libraries: [
        'bem-core @ f4b46ef0590549042d938f7e981df4d14eb4caef',
        'bem-components @ 82301a8af6c15c2849d1f755a24f594de6522251',
        'john-lib'
    ]
```
[Пример кода](https://gist.github.com/innabelaya/8915341) .bem/make.js.

Адрес библиотеки прописываем в `.bem/repo.db.js` файле:

	module.exports = {

    'bem-components' : {
        type     : 'git',
        url      : 'git://github.com/bem/bem-components.git'
    },
    'bem-core' : {
        type     : 'git',
        url      : 'git://github.com/bem/bem-core.git'
    },
      
       ....
       
        'john-lib': {
        type     : 'git',
        url      : 'git://github.com/john-johnson/j.git'
    }

	};
[Пример кода](https://gist.github.com/innabelaya/8915389) .bem/repo.db.js.

Необходимо указать, что данная библиотека должна использоваться при сборке страниц. Это делается в файле `desktop.bundles/.bem/level.js`.

    exports.getConfig = function() {

    return BEM.util.extend(this.__base() || {}, {
        bundleBuildLevels: this.resolvePaths([
                'bem-core/common.blocks',
                'bem-core/desktop.blocks',
                'bem-components/common.blocks',
                'bem-components/desktop.blocks',
                'john-lib/blocks'
            ]
[Пример кода](https://gist.github.com/innabelaya/8915431) desktop.bundles/.bem/level.js.

К сожалению, пока при изменении конфигурации проекта приходится перезапускать bem server. Текущий процесс придётся прервать (`Ctrl+C`) и снова набрать команду `bem server`.
В будущих версиях необходимость перезапуска должна быть устранена.

#Миксы блоков и элементов

Теперь блок **box** можно использовать. Мы применим его к шапке страницы, чтобы добавить белый фон с тенью. Для этого смиксуем блок **head** с блоком **box**, используя метод **mix** в BEMJSON-декларации страницы.

Один из способов смешения — описать метод **mix** во входных данных (BEMJSON).
В данном случае нужно смешать блок **head** с блоком **box**:

    {
        block: 'head',
        mix: [ { block: 'box' } ],
        content: ...
    }
[Пример кода](https://gist.github.com/innabelaya/8931642) index.bemjson.js.

    <!DOCTYPE html>
    <html class="ua_js_yes ua_css_standard">
        <head>...</head>
        <body class="page page__body">
            <div class="page__body-i">
                <div class="head box">
                    <div class="layout">...</div>
                </div>
                <ul class="goods">...</ul>
            </div>
        </body>
    </html>
Запишем блок **box** в зависимости блока **head**.

    $ bem create -l desktop.blocks/ -b head -T deps.js 

```
    ({
        shouldDeps: [
            { block: 'box' }
        ]
    })
```
[Пример кода](https://gist.github.com/innabelaya/8930709) head.deps.js.

<img height="157" width="624" src="https://jing.yandex-team.ru/storage/neige/424422/2014-02-25_1216.png">

Миксовать можно не только блоки, но и элементы с блоками. Не только в BEMJSON-декларации страницы, но и в шаблонах реализации конкретного блока.  

Мы хотим, чтобы каждый товар из списка имел такое же оформление, как и шапка страницы. Для этого в шаблоне блока **goods** смиксуем каждый элемент **item** с блоком **box** из только что подключенной библиотеки.

```
	elem: 'item',
		mods: { new: item.new ? 'yes' : undefined },
		mix: [{ block: 'box' }],
		content: ...
```
[Пример кода](https://gist.github.com/innabelaya/8930835) goods.bemhtml.

    <!DOCTYPE html>
    <html class="i-ua_js_yes i-ua_css_standard">
        <head>...</head>
        <body class="b-page b-page__body">
            <div class="b-page__body-i">
                <div class="head box">
                    <div class="layout">...</div>
                </div>
                <ul class="goods">
                    <li class="goods__item box">...</li>
                    <li class="goods__item box">...</li>
                    <li class="goods__item box">...</li>
                    <li class="goods__item goods__item_new_yes box">...</li>
                    <li class="goods__item box">...</li>
                    <li class="goods__sizer">...</li>
                    ...
                </ul>
            </div>
        </body>
    </html>

![Список товаров в блоке box](https://jing.yandex-team.ru/storage/neige/976769/2014-02-25_1221.png)

#Декларативный JavaScript

##Блоки с JavaScript-функциональностью

Блок **box**, который появился на странице проекта благодаря подключенной сторонней библиотеке, предоставляет также и динамическую JavaScript-функциональность — он умеет сворачиваться.

Для использования этой JavaScript-функциональности в шапке, необходимо изменить описание блока **head**, указав, что блок **box** имеет JavaScript-реализацию. 

    mix: [{ block: 'box', js: true }]
[Пример кода](https://gist.github.com/innabelaya/8930981) goods.bemhtml.

Также разместим внутри блока элемент switcher:

    content: [
        {
            block: 'layout',
            ...
        },
        {
            block: 'box',
            elem: 'switcher'
        }
    ]
[Пример кода](https://gist.github.com/innabelaya/8931082) index.bemjson.js.

Теперь в блоке **head** есть стрелочка, умеющая сворачивать и разворачивать его.

![Стрелочка](http://jing.yandex-team.ru/files/neige/2014-02-25_1227.png)

##Доопределение JavaScript

Расширим предлагаемую библиотекой JavaScript-функциональность блока **box**. Сделаем так, чтобы он сворачивался не только по вертикали, но и по горизонтали. При этом вносить изменения в чужую библиотеку мы не можем. Но благодаря тому, что JavaScript блока написан с использованием декларативного фреймворка [i-bem.js](https://github.com/bem/bem-core/blob/v1/common.docs/i-bem-js/i-bem-js.ru.md), есть возможность изменить (переопределить или доопределить) поведение блока.

    bem create -l desktop.blocks -b box -T js 
В получившемся файле `desktop.blocks/box/box.js` нужно оставить только секцию `onSetMod`, описывающую реакцию на установку модификаторов.

    onSetMod : {

    }
[Пример кода](https://gist.github.com/4195865) box.js.

В данном случае нужно реагировать на установку и снятие модификатора **closed**:

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
[Пример кода](https://gist.github.com/4195879) box.js.

#Создание новых страниц

Страницы — это тоже блоки, но на уровне переопределения `desktop.bundles`. Поэтому для их создания тоже можно воспользоваться командой `bem create`.  
Создадим страницу `contact`:

    $ bem create -l desktop.bundles -b contact
Флаг `-T` можно не указывать, потому что `bem create` благодаря настройкам уровня `desktop.bundles` знает, что создаваемые на этом уровне блоки должны быть представлены в технологии BEMJSON. Таким образом, bem-tools создает файл `desktop.bundles/contact/contact.bemjson.js` с минимальным содержимым для страницы.

Новую страницу можно посмотреть по адресу http://localhost:8080/desktop.bundles/contact/contact.html.  
`bem server` соберет ее HTML-представление, JS- и CSS-файлы в момент первого открытия в браузере.

#Полная сборка проекта

Все время, пока мы разрабатывали проект, работал bem server и пересобирал только измененные части проекта, которые необходимы при обновлении страницы в браузере.

Для запуска проекта нужна его полная сборка, вне зависимости от того, изменилось что-то или нет. Для этого можно воспользоваться командой `bem make`:

    $ bem make

#Благодарность
Автор статьи благодарит tyv и gela-d за подготовку разметки сайта.














