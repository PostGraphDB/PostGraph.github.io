---
layout: default
title: Network Types
nav_order: 6
description: 'Network Datatypes'
---
# Network Types

Cypher includes network data types and functions that are inspired by PostgreSQL's network data types and functions. These capabilities allow you to work with network-related data within your graph database.

# Network Data Types

## Inet 
n Cypher, the inet datatype represents an IP address or a range of IP addresses in IPv4 or IPv6 format. It is a versatile data type that allows for the manipulation and comparison of IP addresses, network masks, and CIDR notation. The inet datatype supports various operators and functions for working with IP addresses, making it useful for tasks such as network analysis and filtering.

The input format for the inet data type is as follows:
- For IPv4 addresses: `x.x.x.x` where each `x` is a decimal number from 0 to 255 representing the four octets of the IPv4 address.
- For IPv6 addresses: `x:x:x:x:x:x:x:x` where each `x` represents a hexadecimal digit in the eight groups separated by colons.
- For CIDR notation: `x.x.x.x/y` for IPv4 or `x:x:x:x:x:x:x:x/y` for IPv6, where `y` is the number of bits in the netmask.

##### Examples:
| ip type     | input               |
| ----------- | ------------------- |
| IPv4        | 192.168.0.1         |
| IPv6        | 2001:0db8:85a3::1   |
| IPv4 w/cidr | 192.168.0.0/24      |
| IPv6 w/cidr | 2001:0db8:85a3::/48 |



