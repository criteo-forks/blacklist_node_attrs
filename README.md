Description
===========

This cookbook provides a library that allows you to set a blacklist of
node attributes that should not be saved on the server. All of the
attributes are still available throughout the chef run, but those
specifically listed will not be saved to the server.

Requirements
============

Chef 0.9.0+

Requires chef-client and a Chef Server - this cookbook doesn't make
sense, nor work, with chef-solo.

Works on any platform.

Attributes
==========

`node[:blacklist]` provides a map of node attributes to store. The
defaults are provided by this cookbook, and the map is:

    node.default[:blacklist] = {
    }

This cookbook honors the fact that attributes are set at different
precedence levels.

Usage
=====

Upload the cookbook, and make sure that it is included as a dependency
in another cookbooks metadata, or that the recipe (which does nothing)
is included in the role.

Whenever node.save is called, such as at the end of the run, the
blacklist will be applied.

Operation
=======
 * Key has either a Boolean value or a Hash as content
   * Key == "xxx"
     * The key is a string pattern to match on, as per knife search
     * Wildcards are:
       * '*' for 0 or more characters
       * '?' for a single character
   * Boolean == true
     * Prune entire subtree *INCLUSIVE* of this key
   * Boolean == false
     * Prune entire subtree *EXCLUSIVE* of this key
   * Hash == {}
     * Descend to next level and don't prune this key

```text
Blacklist.filter(
{
  "network" => {
    "interfaces" => {
      "tapf7db623c-1b" => {},
      "nldev.618" => {},
      "tapf7db623c-1c" => {},
      "tapf7db623c-1a" => {},
      "nldev.617" => {"addresses" => "all"},
      "nldev.619" => { "a" => "b" }
    }
  }
}
,
{
  "network" => {
    "interfaces" => {
      "tap*" => true,
      "*" => {
        "addresses" => false,
        "mtu" => false,
        "*" => true
      }
    }
  }
}


Will return:
{
  "network" => {
    "interfaces" => {
      "nldev.618" => {},
      "nldev.617" => {"addresses" => ""},
      "nldev.619" => {}
    }
  }
}
```

License and Author
==================

Author:: Jon-Paul Sullivan (<jonpaul.sullivan@hp.com>)

Copyright:: 2013, Hewlett-Packard Development Company, L.P. (<legal@hp.com>)

This was derived from the whitelist-node-attrs cookbook:
https://github.com/opscode-cookbooks/whitelist-node-attrs

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
