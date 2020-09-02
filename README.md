你好！
很冒昧用这样的方式来和你沟通，如有打扰请忽略我的提交哈。我是光年实验室（gnlab.com）的HR，在招Golang开发工程师，我们是一个技术型团队，技术氛围非常好。全职和兼职都可以，不过最好是全职，工作地点杭州。
我们公司是做流量增长的，Golang负责开发SAAS平台的应用，我们做的很多应用是全新的，工作非常有挑战也很有意思，是国内很多大厂的顾问。
如果有兴趣的话加我微信：13515810775  ，也可以访问 https://gnlab.com/，联系客服转发给HR。
# dyno

[![Build Status](https://travis-ci.org/icza/dyno.svg?branch=master)](https://travis-ci.org/icza/dyno)
[![GoDoc](https://godoc.org/github.com/icza/dyno?status.svg)](https://godoc.org/github.com/icza/dyno)
[![Go Report Card](https://goreportcard.com/badge/github.com/icza/dyno)](https://goreportcard.com/report/github.com/icza/dyno)
[![codecov](https://codecov.io/gh/icza/dyno/branch/master/graph/badge.svg)](https://codecov.io/gh/icza/dyno)

Package dyno is a utility to work with _dynamic objects_ at ease.

Primary goal is to easily handle dynamic objects and arrays (and a mixture of these)
that are the result of unmarshaling a JSON or YAML text into an `interface{}`
for example. When unmarshaling into `interface{}`, libraries usually choose
either `map[string]interface{}` or `map[interface{}]interface{}` to represent objects,
and `[]interface{}` to represent arrays. Package dyno supports a mixture of
these in any depth and combination.

When operating on a dynamic object, you designate a value you're interested
in by specifying a _path_. A path is a _navigation_; it is a series of map keys
and `int` slice indices that tells how to get to the value.

Should you need to marshal a dynamic object to JSON which contains maps with
`interface{}` key type (which is not supported by `encoding/json`), you may use
the `ConvertMapI2MapS` converter function.

The implementation does not use reflection at all, so performance is rather good.

### Supported Operations

- Get a (typed) value denoted by a path: [Get](https://godoc.org/github.com/icza/dyno#Get), [GetInt](https://godoc.org/github.com/icza/dyno#GetInt),  [GetString](https://godoc.org/github.com/icza/dyno#GetString), [GetSlice](https://godoc.org/github.com/icza/dyno#GetSlice), [GetMapI](https://godoc.org/github.com/icza/dyno#GetMapI), [GetMapS](https://godoc.org/github.com/icza/dyno#GetMapS)

- Get a typed value with built-in conversion / parsing: [GetInteger](https://godoc.org/github.com/icza/dyno#GetInteger), [GetFloating](https://godoc.org/github.com/icza/dyno#GetFloating)

- Specialized get for maps with `string` keys: [SGet](https://godoc.org/github.com/icza/dyno#SGet)

- Set a value denoted by a path: [Set](https://godoc.org/github.com/icza/dyno#Set)

- Specialized set for maps with `string` keys: [SSet](https://godoc.org/github.com/icza/dyno#SSet)

- Append value(s) to a slice denoted by a path: [Append](https://godoc.org/github.com/icza/dyno#Append), [AppendMore](https://godoc.org/github.com/icza/dyno#AppendMore)

- Delete a key from a map or an element from a slice denoted by a path: [Delete](https://godoc.org/github.com/icza/dyno#Delete)

- Convert maps with `interface{}` keys to maps with `string` keys: [ConvertMapI2MapS](https://godoc.org/github.com/icza/dyno#ConvertMapI2MapS)

### Example

Let's see a simple example editing a JSON text to mask out a password. This is
a simplified version of the [`Example_jsonEdit`](https://godoc.org/github.com/icza/dyno#example-package--JsonEdit) example function:

	src := `{"login":{"password":"secret","user":"bob"},"name":"cmpA"}`
	var v interface{}
	if err := json.Unmarshal([]byte(src), &v); err != nil {
		panic(err)
	}
	// Edit (mask out) password:
	if err = dyno.Set(v, "xxx", "login", "password"); err != nil {
		fmt.Printf("Failed to set password: %v\n", err)
	}
	edited, err := json.Marshal(v)
	fmt.Printf("Edited JSON: %s, error: %v\n", edited, err)

Output will be:

	Edited JSON: {"login":{"password":"xxx","user":"bob"},"name":"cmpA"}, error: <nil>
