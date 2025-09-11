# AWS Regions and Availability Zones

## AWS Regions
- Independant phisical location in the world (i.e. `us-east-1`)
- The number of regions is determined and limited (38 on sep/2025), but is constantly expanding
- Every region consists of two or more Availability Zones
- Multiple Regions would be useful only for prevention of giant natural disasters, when an entire region is damaged

## Availability Zones
- Availability Zones are inside AWS Regions
- Composed of one or more data centers
- There are 120 Availability Zones in sep/2025, but constantly expanding
- An availability Zone has redudant power, networking and connectivity
- Multiple AZs can provide high availability to an application, given that each AZ is isolated phisically to the other, so if there is a failure in one AZ, the other can still be up providing resources
