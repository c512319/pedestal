# Pedestal Changelog

**NOTE:** Whenever upgrading versions of pedestal-app, please be sure to clean your project's `out` directory by running `lein clean`.

## 0.4.0 - April 17, 2015

 * Tree-based router (used by default)
 * Open routing (users can set their own router)
 * Robust error-handler interceptor (pattern-matching across all interceptors)
 * Updated Interceptor API (built around a protocol / open for extension)
 * Updated documentation samples
 * Bug fixes and small enhancements to the testing tools

For a full list of changes, please see this comparison of [0.3.1...0.4.0](https://github.com/pedestal/pedestal/compare/0.3.1...0.4.0).

## 0.3.1 - October 9, 2014

 * Container specific functionality is tucked behind a protocol
 * Immutant added as a supported platform type
 * NIO support added for Jetty and Immutant
 * SSE now passes along the original context to the `start-fn`
 * Map-based terse route format added
 * Servlet integration uses Clojure Java API instead of RT
 * Transit support added for requests and responses

For a full list of changes, please see this comparison of [0.3.0...0.3.1](https://github.com/pedestal/pedestal/compare/0.3.0...0.3.1).

## 0.3.0 - June 20, 2014

 * Performance optimizations and new connection code
 * core.async channel-based SSE
 * Better/cleaner SSE heartbeat threadpool
 * Jetty9, Tomcat8, Clojure 1.6, Servlet 3.1
 * Open HTTP verb smuggling
 * CSRF-protection interceptor in the default interceptors (you need to set options for it to turn on)
 * Sessions are http-only by default (pushed up to Ring)
 * Secure-headers interceptor in the default interceptors
 * Servlet filters can be dropped directly into a service-map definition
 * Namespace refactor
 * Simpler template and less dev-tool trickery
 * New public API exposed for building, composing, starting, and stopping services
 * Dependencies upgraded
 * Bug fixes

Details will emerge in an upcoming blog post and within the guides and docs here.

For a full list of changes, please see this comparison of [0.2.2...0.3.0](https://github.com/pedestal/pedestal/compare/0.2.2...0.3.0).

## 0.2.2 - November 5, 2013

Bug fix release preceding some exciting (and breaking) 0.3.0 changes. Unless any major bugs are discovered, no further work will continue on the 0.2.x stream.

For a full list of changes, please see this comparison of [0.2.1...0.2.2](https://github.com/pedestal/pedestal/compare/0.2.1...0.2.2).

## 0.2.1 - September 1, 2013

Small bug fix for app-template.

For a full list of changes, please see this comparison of [0.2.0...0.2.1](https://github.com/pedestal/pedestal/compare/0.2.0...0.2.1).

## 0.2.0 - August 30, 2013

This is a big one folks. Developer tooling has moved from generated projects into
the app- and service-tools libraries.

**If you want to take advantage of all the super-awesome new features then further action is required.**
See the respective changelogs in App and Service for more info.

### App

* Developer tooling has moved in its entirety to app-tools.

  Notable improvements:
    * Tooling functions are automatically included when running `lein repl`.
    * App configuration can be reloaded with `(reload-config)`
    * Future upgrades to tooling will be incorporated more easily.

  Additionally, app configuration is specified by an EDN file (instead of an
  executable Clojure file.) New projects will make use of this feature, but
  pre-0.2.0 generated projects will not.

  **You do not *have* to make this upgrade, but we *suggest* you do.**

  The easiest way to upgrade is to re-generate your application with the 0.2.0
  app-template and transfer your existing code into it. It is possible, however,
  to migrate your existing application.

  **How to migrate a 0.1.x project to 0.2.0:**
      1. Upgrade your project's pedestal-app dependencies to version `"0.2.0"`
      2. Remove the `dev/` folder.
      3. `$ touch config/user.clj` -- This is needed for `:repl-options` with `:init user` to work.
      4. Update your `project.clj`'s `:main` and `:repl-options` keys [like so](https://github.com/pedestal/pedestal/blob/4a148bf23c255baee420d7b725677b98de325120/app-template/src/leiningen/new/pedestal_app/project.clj#L14-L23).
      5. Add the [piggieback dependency](https://github.com/pedestal/pedestal/blob/4a148bf23c255baee420d7b725677b98de325120/app-template/src/leiningen/new/pedestal_app/project.clj#L9) to your `project.clj` (`[com.cemerick/piggieback "0.1.0"]`).
      6. Update your application's `config.clj` file. There are two ways to do this...
          1. *If you have not modified your `config.clj`: Generate a new
             application with the same name as your existing application, and
             steal the `config.edn` file from it.
          2. *If you have changed your `config.clj`, or you're a glutten for
             punishment* perform these steps (looking like
             [this](https://github.com/pedestal/pedestal/blob/4a148bf23c255baee420d7b725677b98de325120/app-template/src/leiningen/new/pedestal_app/config/config.edn)
             is our goal):
              1. `mv config/config.clj config/config.edn`
              2. Remove the `ns` declaration.
              3. Unwrap the `configs` def into a raw map.
              4. Remove quotes from quoted namespaces. Look in :main and :renderer keys.
              5. At path `[:build :watch-files]`,
                 `(compile/html-files-in "app/templates")` should become a map
                 of tags to regex pattern strings like
                 `{:html ["^app/templates"]}`. Note these are **string** regex
                 patterns, not regexps--regexps aren't supported by EDN.
              6. At path `[:build :triggers]`, existing strings should be
                 converted to string regex patterns. For example, the original
                 `{:html ["project-name/rendering.js"]}` would become
                 `{:html ["project-name//rendering\\.js$"]}`.

* Tooling's `cljs-repl` is now provided by Chas Emerick's [Piggieback](https://github.com/cemerick/piggieback).
* Tooling's `cljs-repl` is now more clear about usage. This fixes [#93](https://github.com/pedestal/pedestal/issues/93), [#90](https://github.com/pedestal/pedestal/issues/90).
* App's ClojureScript dependency has been bumped to r1835. Namespaced keywords are now allowed (`::msg/topic`)!
* Logging in the browser is now grouped. [#95](https://github.com/pedestal/pedestal/pull/95)
* The template now includes a `:ui` aspect for rendering the `simulated` behavior. [#184](https://github.com/pedestal/pedestal/pull/184), [#187](https://github.com/pedestal/pedestal/pull/187)
* Added the `:read-as` option for `msg/param`. Setting to `:data` causes collected values to be parsed by the Clojure reader. [#166](https://github.com/pedestal/pedestal/pull/166)*

### Service

* Service tooling has moved out of generated projects into a service-tools library.

  Notable improvements:

  * Tooling functions are automatically included when running `lein repl`.
  * Future upgrades to tooling will be incorporated more easily.

  There is a bit of migration necessary to move existing projects to 0.2.0 tooling.
  **You don't *have* to make this change, but we *suggest* you do**.

  **How to migrate a 0.1.x project to 0.2.0:**
      1. Update your project's dependencies to match the [new ones](https://github.com/pedestal/pedestal/blob/0.2.0/service-template/src/leiningen/new/pedestal_service/project.clj):
         1. Upgrade your project's pedestal-service dependencies to version `"0.2.0"`.
         2. Add `[io.pedestal/pedestal.service-tools "0.2.0"]` as a dependency.
         3. Remove logback and slf4j logging dependencies.
      2. Remove the `dev/` folder
      3. `$ touch config/user.clj` -- This is needed for `:repl-options` with `:init user` to work.
      4. Update your `project.clj`'s `:main` and `:repl-options` keys to match
         [the new template project.clj](https://github.com/pedestal/pedestal/blob/4a148bf23c255baee420d7b725677b98de325120/service-template/src/leiningen/new/pedestal_service/project.clj#L18-L32).
      5. Pare down your src/**/server.clj file to match
         [the new template server.clj](https://github.com/pedestal/pedestal/blob/4a148bf23c255baee420d7b725677b98de325120/service-template/src/leiningen/new/pedestal_service/server.clj).

* Service now uses [Cheshire](https://github.com/dakrone/cheshire)
  instead of [clojure.data.json](https://github.com/clojure/data.json) for constructing
  [`json-response`s](../1eeff6a56c20a4cb617148a7d2f22773d0e640ee/service/src/io/pedestal/service/http.clj#L49)
  and [parsing json
  bodies](../1eeff6a56c20a4cb617148a7d2f22773d0e640ee/service/src/io/pedestal/service/http/body_params.clj#L79). [#162](https://github.com/pedestal/pedestal/issues/162)

  **This change *does* eliminate some JSON parsing options that were previously
  possible in 0.1.10.** Specifically the following options are no longer supported:

    * `:eof-error?` - "If true (default) will throw exception if the stream is empty."
    * `:eof-value` - "Object to return if the stream is empty and eof-error? is false. Default is nil."

  If your application makes use of these options you will need to construct a
  `body-params` interceptor with a `parser-map` where you have swapped in your
  own `#"^application/json"` key with a `custom-json-parser` similar to the [old
  version](../7d9d3a028b9529963ec1f46633ef10a73054d140/service/src/io/pedestal/service/http/body_params.clj#L78).

  That might look something like this:

    ```clojure
    ;; In a utility namespace
    (require '[io.pedestal.service.http.body-params :as bp])
    (defn old-style-json-parser ...)

    (def my-body-params (bp/body-params (-> (bp/default-parser-map)
                                            (assoc #"^application/json" (old-style-json-parser)))))
    ```

* The default behavior of the `body-params` interceptor now keywordizes JSON
  keys. To retain the old behavior, create a `body-params` interceptor like so:

    ```clojure
    (require '[io.pedestal.service.http.body-params :as bp])
    (def string-keys-body-params (bp/body-params (bp/default-parser-map :json-options {:key-fn nil})))
    ```
* A bug with CORS headers has been fixed.
* The default MIME type has been returned to text/plain (it was a bug that it changed to octet-stream.)

### Miscellaneous bug-fixes and improvements

For a full list of changes, please see this comparison of [0.1.10...0.2.0](https://github.com/pedestal/pedestal/compare/0.1.10...0.2.0).


## 0.1.10 - July 5, 2013

### App

* The messages queue is now a priority queue. Specify `msg/priority :high` in a message for that message to be processed before all other unadorned messages. [2495b9a5](https://github.com/pedestal/pedestal/commit/2495b9a5d760bf39cf8957b20d022dac951b15a4)
* Various improvements that make it easier to run in a web worker (more on that to follow, :wink:)

### Service

* `io.pedestal.service.http.route/url-for` now accepts a `:fragment` option for specifying URL fragments (i.e `http://example.com/#foobars). [#85](https://github.com/pedestal/pedestal/pull/85)
* `io.pedestal.service.http.body-params`'s edn and json parsers can now be configured (both alone and as part of `default-parser-map`). [#96](https://github.com/pedestal/pedestal/pull/96), [#97](https://github.com/pedestal/pedestal/pull/97), [#98](https://github.com/pedestal/pedestal/pull/98)

### Miscellaneous bug-fixes and improvements

For a full list of changes, please see this comparison of [0.1.9...0.1.10](https://github.com/pedestal/pedestal/compare/0.1.9...0.1.10).


## 0.1.9 - June 14, 2013

### General

* All Pedestal libraries now properly depend on Clojure 1.5.1.

### App

* The dataflow engine now properly reports changes when nodes have nil or falsey values. [#78](https://github.com/pedestal/pedestal/pull/78)
* Messages that throw exceptions during processing now log an error message.
* Templates can now insert content at a specific index with `io.pedestal.app.render.push.templates/insert-t`. [#81](https://github.com/pedestal/pedestal/pull/81)
* Generated `dev/dev.clj` now uses `(start)` instead of `(run)`, bringing it in line with pedestal-service. [#84](https://github.com/pedestal/pedestal/pull/84)

### Miscellaneous bug-fixes and improvements

For a full list of changes, please see this comparison of [0.1.8...0.1.9](https://github.com/pedestal/pedestal/compare/0.1.8...0.1.9).

## 0.1.8 - May 29, 2013

### General

* App and service templates now allow creating projects with namespaces [#68](https://github.com/pedestal/pedestal/issues/68).

            $ lein new pedestal-app com.example/foo
            ... creates foo/ with src/com/example/foo/*.clj

### Service

* Corrected a test error in the generated service template project.

### Miscellaneous bug-fixes and improvements

For a full list of changes, please see this comparison of [0.1.7...0.1.8](https://github.com/pedestal/pedestal/compare/0.1.7...0.1.8).

## 0.1.7 - May 24, 2013

### App

* The new simplified dataflow engine is here! We're working to update our documentation and samples now. Stay informed by following [@pedestal_team](http://twitter.com/pedestal_team) on twitter.
    * Existing applications will continue to function normally.
* `io.pedestal.app.templates/dtfn` now allows for more than one data field [#60](https://github.com/pedestal/pedestal/issues/60).

### Service

* Empty EDN responses are now handled gracefully [#69](https://github.com/pedestal/pedestal/pull/69).
* Resources for services can now be drawn from `resources/` [#51](https://github.com/pedestal/pedestal/pull/51).
* Typo fixes in HTML and JSON interceptors [#72](https://github.com/pedestal/pedestal/pull/72).
* Corrected a few places the `Content-Type` header was not being set properly [#58](https://github.com/pedestal/pedestal/issues/58), [#65](https://github.com/pedestal/pedestal/pull/65).


### Miscellaneous bug-fixes and improvements

For a full list of changes, please see this comparison of [0.1.6...0.1.7](https://github.com/pedestal/pedestal/compare/0.1.6...0.1.7).


## 0.1.6 - May 03, 2013

### Service

* Context paths now work with JBoss
* It is now possible to specify TCP port in routes (default: 8080). Specified ports will also be reflected in generated URL.

        (defroutes routes
          [[:app1 8080
            ["/" {:get app1-root}]]
           [:app2 8181
            ["/" {:get app2-root}]]]

### Miscellaneous bug-fixes and improvements

For a full list of changes, please see a comparison of [0.1.5...0.1.6](https://github.com/pedestal/pedestal/compare/0.1.5...0.1.6).

## 0.1.4/0.1.5 - April 05, 2013

We encountered a bug deploying version 0.1.4 so that release was re-done as version 0.1.5

### App

* `lein clean` now correctly deletes `out` directory.

### Service

* Added `text-as-html` and `data-as-json` interceptors.
* Newly generated service apps assume content is `text/html` if not specified otherwise.


### Miscellaneous bug-fixes and improvements

Special thanks to [@ddeaguiar](https://github.com/ddeaguiar) for grammar and
spelling corrections, as well as his help in removing
[lein-marginalia](https://github.com/fogus/lein-marginalia) as a dependency.
We've updated the [documentation](http://pedestal.io/documentation/) with
instructions on how to continue to generate marginalia documentation.

For a full list of changes, please see a comparison of
[0.1.3...0.1.5](https://github.com/pedestal/pedestal/compare/0.1.3...0.1.5).

## 0.1.3 - April 05, 2013

### App

* app-tools now serves every request with header Cache-Control: no-cache ([\#44](https://github.com/pedestal/pedestal/issues/44)) - [@rkneufeld](https://github.com/rkneufeld)

### Service

* Add CORS support to SSE and service template - [@timewald](https://github.com/timewald)

### Miscellaneous bug-fixes and improvements

For a full list of changes, please see a comparison of [0.1.2...0.1.3](https://github.com/pedestal/pedestal/compare/0.1.2...0.1.3).


## 0.1.2 - March 29, 2013

### General

* [Travis CI](https://travis-ci.org/pedestal/pedestal) integration has been enabled ([\#27](https://github.com/pedestal/pedestal/issues/27)) - [@cldwalker](https://github.com/cldwalker)
* lein repl history is now ignored by git ([\#38](https://github.com/pedestal/pedestal/issues/38)) - [@crimeminister](https://github.com/crimeminister)

### App

* app tests no longer have non-deterministic timing problems ([\#39](https://github.com/pedestal/pedestal/issues/39)) - [@brentonashworth](https://github.com/brentonashworth)
* app-tools now plays (more) nicely with Windows ([\#23](https://github.com/pedestal/pedestal/issues/23), [\#29](https://github.com/pedestal/pedestal/issues/29), [\#31](https://github.com/pedestal/pedestal/issues/31)) - [@djpowell](https://github.com/djpowell), [@rkneufeld](https://github.com/rkneufeld)

### Service

* Add support for Heroku ([\#18](https://github.com/pedestal/pedestal/issues/18)) - [@aredington](https://github.com/aredington), [@cldwalker](https://github.com/cldwalker), [@hiredman](https://github.com/hiredman), [@timewald](https://github.com/timewald)
* An HTTP status is now sufficient to bypass the not-found interceptor (whereas it used to require headers) ([\#30](https://github.com/pedestal/pedestal/issues/30), [\#34](https://github.com/pedestal/pedestal/issues/34)) - [@hiredman](https://github.com/hiredman), [@rkneufeld](https://github.com/rkneufeld)
* Removed a number of incorrect :or keys ([\#32](https://github.com/pedestal/pedestal/issues/32)) - [@cldwalker](https://github.com/cldwalker)
* Usage of 'read-string' has been audited and corrected ([\#40](https://github.com/pedestal/pedestal/issues/40)) - [@stuartsierra](https://github.com/stuartsierra)
* io.pedestal.service.http.body-params/set-content-type now uses correct casing for HTTP headers ([\#35](https://github.com/pedestal/pedestal/issues/35)) - [@stuarth](https://github.com/stuarth)
