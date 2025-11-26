# Shortcode <a href="https://gitpod.io/#https://github.com/dracory/shortcode" style="float:right:"><img src="https://gitpod.io/button/open-in-gitpod.svg" alt="Open in Gitpod" loading="lazy"></a>

[![Tests Status](https://github.com/dracory/shortcode/actions/workflows/tests.yml/badge.svg?branch=main)](https://github.com/dracory/shortcode/actions/workflows/tests.yml)
[![Go Report Card](https://goreportcard.com/badge/github.com/dracory/shortcode)](https://goreportcard.com/report/github.com/dracory/shortcode)
[![PkgGoDev](https://pkg.go.dev/badge/github.com/dracory/shortcode)](https://pkg.go.dev/github.com/dracory/shortcode)

## Introduction

Shortcodes are powerful tools that allow you to easily embed interactive elements or create complex layouts on your webpages with just a single line of code. 

This makes them especially convenient for Content Management Systems (CMS) where editors can insert pre-defined components without needing to write complex backend code.

Shortcodes as seen in popular platforms like Wordpress, allow you to effortlessly embed interactive elements and create stunning layouts with just a single line of code.

## Key Benefits of Shortcodes:

- **Simplicity:** Shortcodes provide a user-friendly way to add complex elements without writing HTML or other code.   
- **Flexibility:** They can be used to create custom content blocks, embed external content, and perform various other functions.  
- **Extensibility:** Developers can create custom shortcodes to expand the functionality of a CMS.   

## Installation
```
go get -u github.com/dracory/shortcode
```

## Example

Source file:

```html
<html>
  <body>
[myshortcode id="111"][/myshortcode]
[myshortcode id="222"][/myshortcode]
  </body>
</body>
```

Your Golang Shortcode

```golang
// The shortode function
func myShortcode(content string, args map[string]string) string {
	return "MY SHORTCODE WITH ID " + args["id"]
}

// Use the shortcode
sh, err := NewShortcode(WithBrackets("[", "]"))
parsed := sh.Render(text, "myshortcode", myShortcode)
```

Result

```html
<html>
  <body>
MY SHORTCODE WITH ID 111
MY SHORTCODE WITH ID 222
  </body>
</body>
```

# Example With Request

In this example, the RenderShortocdes function find search the provided content for shortcodes.

If a supported shortcode is found in the content it will render the corresponding widget.

The request is passed to the shortcode and can be used, i.e. to find the authenticated user, path, etc.

```go
func RenderShortcodes(req *http.Request, content string) string {
	shortcodes := map[string]func(*http.Request, string, map[string]string) string{
		"x-latest-blogs":           widgets.NewLatestBlogsWidget().Render,
		"x-course-list":            widgets.NewCourseListWidget().Render,
		"x-flash-message":          widgets.NewFlashMessageWidget().Render,
		"x-language-dropdown":      widgets.NewLanguageDropdownWidget().Render,
		"x-login-form":             widgets.NewLoginFormWidget().Render,
		"x-register-form":          widgets.NewRegisterFormWidget().Render,
		"x-forgot-password-form":   widgets.NewForgotPasswordFormWidget().Render,
		"x-top-menu-user-dropdown": widgets.NewTopMenuDropdownWidget().Render,
		"x-website-header":         widgets.NewWebsiteHeader().Render,
	}

	sh, err := shortcode.NewShortcode(shortcode.WithBrackets("<", ">"))
	if err != nil {
		return content
	}

	for k, v := range shortcodes {
		content = sh.RenderWithRequest(req, content, k, v)
	}

	return content
}
```
