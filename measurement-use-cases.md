# Measurement Use Cases

The group is currently working on developing a privacy preserving ad measurement system. 

There is general agreement that we want such a system to satisfy the following use cases:

# Advertiser Reporting

TODO: fill this in

# Optimization

Ad selection systems often attempt to estimate the probability that if an ad is shown, it will lead to a conversion event. 
More sophisticated systems may also attempt to predict conversion values / counts / categories.

The information produced by "Advertiser Reporting" is already sufficient to make naive predictions (i.e. just estimate the average conversion rate for all users). 
But there is interest in the group to intentionally design the private measurement system to do better than this.

There is general agreement in the group that:

1. This is a use case that we explicitly want to support
2. Within our agreed privacy bounds we would like to design outputs which provide as much utility as possible for this use case
3. We are flexible on the mechanism to support this, and potentially envision a private measurement system which supports multiple modalities intended to support this use case.

