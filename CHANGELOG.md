# Changelog

## 0.5.2 (May 30th, 2009)
* Added class helper methods Yajl::Encoder.encode(obj, io) and Yajl::Parser.parse(io)
 * added tests for the above
* Updated Twitter streaming example to have a less verbose output
* Patch Yajl so encoding can continue as a stream
 * IE: multiple objects encoded onto the same IO
 * added a test for the above
* Set the internal read buffer size back down to 8kb by default
* Added an internal write buffer size (set to 8kb by default) which is used to throttle writes to the output stream
 * This is to fix a major performance bug/issue with the IO#write C method in ruby 1.9.x (I've opened a bug with them about it)
* Fixed a typo in a one-off parsing spec test
* Updated benchmarks to work properly in 1.9 (required removal ActiveSupport benchmarking for now)
* Updated spec tests to respect ridiculous differences in hash key ordering between 1.8 and 1.9


## 0.5.1 (May 25th, 2009)
* added some more tests for the new API
* inlined a couple of hot functions used in parsing for a little speedup
* updates to readme, reflecting changes in API
* version bump to push another gem build

## 0.5.0 (May 25th, 2009)
* Refactored internal API so the caller can specify initialization options for the Parser and Encoder respectively. Two new classes were introduced as a result - Yajl::Parser and Yajl::Encoder. The newly refactored codebase is cleaner, thread-safe and removed all of the hack-code that was trickled around to make things work in the previous implementation. She's much more seaworthy now cap'n!
 * Yajl::Parser.new accepts two options, :allow_comments and :check_utf8 which both default to true
 * Yajl::Encoder.new accepts two options, :pretty and :indent which default to false and "  " respectively
* cleaned up a lot of state code, that to my knowledge prevented yajl-ruby from being used in a thread-safe environment.
* added deprecated messaging to Yajl::Stream.parse and Yajl::Stream.encode - these will likely go away before 0.6.0
* fixed a bug in the chunked http response parser regarding partially received chunks
* added a Twitter Search API example showing off the HttpStream API

## 0.4.9 (May 20th, 2009)
* fixed some parser state bugs surfaced by edge cases
* added support for Chunked HTTP response bodies in Yajl::HttpStream
 * added support for passing a block to Yajl::HttpStream.get that will be used as a callback whenever a JSON object is parsed off the stream (even if there is more than one!)
* added an examples folder, and put an example using the Twitter Streaming API in there to start
* added some more spec tests, this time around Chunked parsing and continuously parsing multiple JSON strings

## 0.4.8 (May 18th, 2009)
* fixed a totally bone-head compilation problem, I created for myself ;)

## 0.4.7 (May 18th, 2009)
* Bundling Yajl sources to remove the need to install them (and CMake) separately (Thank you Lloyd!!!) This means you can now simply install the gem and be off and running
* Added some spec tests for Yajl::HttpStream
* Added some spec tests for Yajl::Stream.encode
* added some more thank you's, where credit's due - in the readme
* updated the unicode.json file to reflect a "real-life" JSON response
* reorganized spec tests into their functional areas
* added an rcov rake task to generate code coverage output

## 0.4.6 (May 17th, 2009)
* Applied a patch from benburkert (http://github.com/benburkert) to fix HTTP Basic Auth in Yajl::HttpStream.get

## 0.4.5 (May 17th, 2009)
* added Yajl::Stream.encode(hash, io)
 * generates a JSON string stream, and writes to IO
 * compressed StreamWriter helpers added as well
* fixed a pretty lame segfault in (x86_64 only?) ubuntu/linux
* changed the compiled extension to have a more specific name (yajl_ext) for easier loading
* removed forced-load of .bundle file, for the rest of the planet aside from OSX users
* added some more benchmarks to compare to other forms of serialization in Ruby
* various readme updates

## 0.4.4 (May 12th, 2009)
* NOTE: Breaking API change:
 * renamed Yajl::GzipStreamReader to Yajl::Gzip::StreamReader
* added Yajl::Bzip2::StreamReader
 * depends on the bzip2-ruby gem if you want to use it, if not Yajl::Bzip2 won't be loaded
* added Yajl::Deflate::StreamReader
 * actually uses Zlib::Inflate for stream decompression
* added parse(io) class methods to Yajl::Gzip::StreamReader and Yajl::Bzip2::StreamReader as a helper for parsing compressed streams.
* updated Yajl::HttpStream to request responses compressed as deflate and bzip2 in addition to gzip
* fixed a bug regarding parsing Integers as Floats (so 123456 would have be parsed and returned as 123456.0)
* fixed a bug which caused a segfault in ruby's GC during string replacement in Yajl::Gzip and Yajl::Bzip2's StreamReader#read methods
* added support for user-specified User-Agent strings in Yajl::HttpStream

## 0.4.3 (May 2nd, 2009)
* adding text/plain as an allowed mime-type for Yajl::HttpStream for webservers that respond with it instead of application/json (ahem...Yelp...)
* renamed specs folder to spec for no reason at all

## 0.4.2 (April 30th, 2009)
* Yajl::HttpStream is now sending "proper" http request headers
* Yajl::HttpStream will request HTTP-Basic auth if credentials are provided in the passed URI
* cleanup requires

## 0.4.1 (April 30th, 2009)
* fixed a typo in the stream.rb benchmark file
* fixed a bug in Yajl::Stream.parse that was causing "strange" Ruby malloc errors on large files, with large strings
* added Yajl::GzipStreamReader as a wrapper around Zlib::GzipReader to allow for standard IO#read behavior
 * this allows Yajl::Stream to read off of a Gzip stream directly
 
## 0.4.0 (April 29th, 2009)
* NOTE: Breaking API change:
 * refactored Stream parsing methods out of Yajl::Native into Yajl::Stream
 * removed Yajl::Native namespace/module
* Addition of Yajl::HttpStream module
 * This module is for streaming JSON HTTP responses directly into Yajl (as they're being received) for increased awesomeness
 * it currently supports basic get requests with Yajl::HttpStream.get(uri)
 * it also supports (and prefers) output compressed (gzip) responses
* Addition Yajl::Chunked module
 * This module is for feeding Yajl JSON pieces at a time, instead of an entire IO object
 * This works very well in environments like an EventMachine app where data is received in chunks by design
* decreased read buffer for Yajl::Stream from 8kb to 4kb

## 0.3.4 (April 24th, 2009)
* turned Unicode checks back on in the Yajl parser now that it's fixed (thanks Lloyd!)
 * this also bumps the yajl version dependency requirement to 1.0.4
* better guessing of Integer/Float from number found instead of just trying to create a BigNum no matter what
* changed extconf.rb to fail Makefile creation if yajl isn't found
* added a test to check for parsing Infinity due to a Float overflow

## 0.3.3 (April 24th, 2009)
* 1.9 compatibility

## 0.3.2 (April 24th, 2009)
* version bump: forgot to include yajl.c in the gem

## 0.3.1 (April 23rd, 2009)
* fixed borked gemspec

## 0.3.0 (April 23rd, 2009)
* slight refactor of ActiveSupport tests to better reflect how they actually exist in ActiveSupport
* typo correction in the changelog which had the years in 2008
* added some initial spec tests
 * ported some from ActiveSupport to ensure proper compatibility
 * included 57 JSON fixtures to test against, all of which pass
* changed parser config to not check for invalid unicode characters as Ruby is going to do this anyway (?). This resolves the remaining test failures around unicode.
* changed how the parser was dealing with numbers to prevent overflows
* added an exception class Yajl::ParseError which is now used in place of simply printing to STDERR upon a parsing error
* renamed a couple of JSON test files in the benchmark folder to better represent their contents
* misc README updates

## 0.2.1 (April 23rd, 2009)
* fixed parsing bug - also fixed failing ActiveSupport test failures (except for the unicode one, which is an issue in Yajl itself)

## 0.2.0 (April 22nd, 2009)
* updated gemspec and README

## 0.1.0 (April 21st, 2009)
* initial release - gemified