# go-i18n [![Build Status](https://travis-ci.org/nicksnyder/go-i18n.svg?branch=master)](http://travis-ci.org/nicksnyder/go-i18n) [![Report card](https://goreportcard.com/badge/github.com/roylee0704/go-i18n)](https://goreportcard.com/report/github.com/roylee0704/go-i18n) [![Sourcegraph](https://sourcegraph.com/github.com/roylee0704/go-i18n/-/badge.svg)](https://sourcegraph.com/github.com/roylee0704/go-i18n?badge)

go-i18n is a Go [package](#package-i18n) and a [command](#command-goi18n) that helps you translate Go programs into multiple languages.

* Supports [pluralized strings](http://cldr.unicode.org/index/cldr-spec/plural-rules) for all 200+ languages in the [Unicode Common Locale Data Repository (CLDR)](http://www.unicode.org/cldr/charts/28/supplemental/language_plural_rules.html).
  * Code and tests are [automatically generated](https://github.com/roylee0704/go-i18n/tree/master/i18n/language/codegen) from [CLDR data](http://cldr.unicode.org/index/downloads)
* Supports strings with named variables using [text/template](http://golang.org/pkg/text/template/) syntax.
* Supports message files of any format (e.g. JSON, TOML, YAML, etc.).
* [Documented](http://godoc.org/github.com/roylee0704/go-i18n) and [tested](https://travis-ci.org/nicksnyder/go-i18n)!

## Versions

* v1 is available at 1.x.x tags.
* v2 is available at 2.x.x tags.

This README always documents the latest version (i.e. v2).

## Package i18n [![GoDoc](http://godoc.org/github.com/roylee0704/go-i18n?status.svg)](http://godoc.org/github.com/roylee0704/go-i18n/v2/i18n)

The i18n package provides support for looking up messages according to a set of locale preferences.

```go
import "github.com/roylee0704/go-i18n/v2/i18n"
```

Create a Bundle to use for the lifetime of your application.

```go
bundle := &i18n.Bundle{DefaultLanguage: language.English}
```

Create a Localizer to use for a set of language preferences.

```go
func(w http.ResponseWriter, r *http.Request) {
    lang := r.FormValue("lang")
    accept := r.Header.Get("Accept-Language")
    localizer := i18n.NewLocalizer(bundle, lang, accept)
}
```

Use the Localizer to lookup messages.

```go
localizer.MustLocalize(&i18n.LocalizeConfig{
    DefaultMessage: &i18n.Message{
        ID: "PersonCats",
        One: "{{.Name}} has {{.Count}} cat.",
        Other: "{{.Name}} has {{.Count}} cats.",
    },
    TemplateData: map[string]string{
        "Name": "Nick",
        "Count": 2,
    },
    PluralCount: 2,
}) // Nick has 2 cats.
```

It requires Go 1.9 or newer.

## Command goi18n [![GoDoc](http://godoc.org/github.com/roylee0704/go-i18n?status.svg)](http://godoc.org/github.com/roylee0704/go-i18n/v2/goi18n)

The goi18n command manages message files used by the i18n package.

```
go get -u github.com/roylee0704/go-i18n/v2/goi18n
goi18n -help
```

Use `goi18n extract` to create a message file that contains the messages defined in your Go source files.

```toml
# en.toml
[PersonCats]
description = "The number of cats a person has"
one = "{{.Name}} has {{.Count}} cat."
other = "{{.Name}} has {{.Count}} cats."
```

Use `goi18n merge` to create message files for translation.

```toml
# translate.es.toml
[PersonCats]
description = "The number of cats a person has"
hash = "sha1-f937a0e05e19bfe6cd70937c980eaf1f9832f091"
one = "{{.Name}} has {{.Count}} cat."
other = "{{.Name}} has {{.Count}} cats."
```

Use `goi18n merge` to merge translated message files with your existing message files.

```toml
# active.es.toml
[PersonCats]
description = "The number of cats a person has"
hash = "sha1-f937a0e05e19bfe6cd70937c980eaf1f9832f091"
one = "{{.Name}} tiene {{.Count}} gato."
other = "{{.Name}} tiene {{.Count}} gatos."
```

Load the active messages into your bundle.

```go
bundle.RegisterUnmarshalFunc("toml", toml.Unmarshal)
bundle.MustLoadMessageFile("active.es.toml")
```

## For more information and examples:

* Read the [documentation](http://godoc.org/github.com/roylee0704/go-i18n/v2/i18n).
* Look at the [code examples](https://github.com/roylee0704/go-i18n/blob/master/v2/i18n/example_test.go) and [tests](https://github.com/roylee0704/go-i18n/blob/master/v2/i18n/localizer_test.go).
* Look at an example [application](https://github.com/roylee0704/go-i18n/tree/master/v2/example).

## License

go-i18n is available under the MIT license. See the [LICENSE](LICENSE) file for more info.
