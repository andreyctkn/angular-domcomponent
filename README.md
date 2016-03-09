[![Build Status](https://travis-ci.org/AndreyUtka/angular-domcomponent.svg?branch=master)](https://travis-ci.org/AndreyUtka/angular-domcomponent)

### Angular DOM component ###

Why?

For angular.js 1.x projects which use ES2015 or class-based definition model. 
You can use [babel](https://babeljs.io/) or [coffeescript](http://coffeescript.org/) or [typescript](http://www.typescriptlang.org/).

if you declare angular service with class definition maybe it looks like this:

```
export class ServerService {

  static $inject = ['$http'];

  private BASE_URL = '';

  constructor(private $http: angular.IHttpService) { }

  public get(path, id?) {
    return this.$http.get(this.BASE_URL + path)
      .then(response => response.data);
  }
}

angular.module("app", []).service("ServerService", ServerService)
```

It looks very clear) but if we try to declare in similar way angular directive:

```
import {IHelloScope} from './hello.model';
import IDirectiveFactory = ng.IDirectiveFactory;

export class HelloComponent {
    displayName: String;
    static selector = 'hello';
    static directiveFactory: IDirectiveFactory = () => {
        return {
            restrict: 'E',
            link: (scope: IHelloScope) => new HelloComponent(scope),
            template: require('./hello.html')
        };
    };

    constructor(scope: IHelloScope) {
        scope.displayName = 'firstName';
    }
}

angular.module("app", []).service(HelloComponent.selector, HelloComponent.directiveFactory)
```
and inject some services..

```
import {IHelloScope} from './hello.model';
import IDirectiveFactory = ng.IDirectiveFactory;

export class HelloComponent {
    static selector = 'hello';
    static directiveFactory = (): ng.IDirectiveFactory => {
         let directive = ($animate, $q, $timeout): ng.IDirective => {
            return {
                restrict: "E",
                template: require('./hello.html'),
                link: (
                    scope: IModalScope,
                    element: ng.IAugmentedJQuery,
                    attrs: ng.IAugmentedJQuery,
                    controller: any,
                ) => new HelloComponent(scope, $animate, $q, $timeout)
            };
        };

        directive.$inject = ["animate", "$q", "$timeout"];

        return directive;
    };

    constructor(scope: IHelloScope, $animate, $q, $timeout) {
        scope.displayName = 'firstName';
    }
}

angular.module("app", []).service(HelloComponent.selector, HelloComponent.directiveFactory())
```

It looks very bulky..

In angular 1.5 appears [the component](https://toddmotto.com/exploring-the-angular-1-5-component-method/) - great, but it is a simple wrapper over the classic Directive,
without possible to inject services and link function.

Angular DOM component - it's also a wrapper of Directive but with:
- possible to inject services
- link function will call constructor of component.
- all setting must be a static fields

![alt tag](http://serialobzor.ru/upload/blogs/72793f9c2fac307f0c2b1b340592eedc.jpg)

Finally:

```
import {IHelloScope} from './hello.model';

export class HelloComponent {
    static selector = 'hello';
    static restrict = "E";
    static template = require('./hello.html');
    static $inject = ["animate", "$q", "$timeout"];

    constructor(scope: IHelloScope, $animate, $q, $timeout) {
        scope.displayName = 'firstName';
    }
}

angular.module("app", []).service(HelloComponent.selector, HelloComponent)
```
If you have any idea or issue please welcome.
