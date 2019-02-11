---
nav_title: Filters
page_order: 4
---

# Filters

{% raw %}

[Filters][12] can be used to reformat static or dynamic content. For example, you can use a filter to reformat a string from uppercase to lowercase or to perform mathematical operations.

Filters should be placed within an output tag `{{ }}` and should be separated from the value that you'd like to reformat with a pipe character `|`.

For example, the output of `{{"Big Sale" | upcase}}` is "BIG SALE".

Or, let's say that you're running a campaign where you're offering customers 50 rewards points if they make a purchase and you've stored their current rewards balance as a custom attribute.

You can use the add filter to show customers what their rewards balance would be if they made an additional purchase:

```liquid
Hey Marion, make a purchase today to earn 50 points and bring your rewards balance to {{custom_attribute.${current_rewards_balance} | plus: 50}}.
```

## Math Filters

Math filters allow you to complete basic mathematical operations in the message composer. For example, you can add or subtract static values or variables to and from numeric personalizable attributes, other static values and or other variables. In this example, let's assume that the value of `current_rewards_balance` is 50.

Input:

```liquid
Hey Marion, make a purchase today to earn 50 pts and bring your rewards balance to {{custom_attribute.${current_rewards_balance} | plus: 50}}.
```

Output:

```liquid
Hey Marion, make a purchase today to earn 50 pts and bring your rewards balance to 100.
```

You cannot perform mathematical operations with two custom attributes.

```liquid
{{custom_attribute.${current_rewards_balance} | plus: {{custom_attribute.${giftcard_balance}}}}}
```

You must first define one of the custom attributes as a variable and then perform the operation with the variable and the remaining attribute. Use a [variable tag][30] to capture the value of one of the custom attributes:

```liquid
{% assign balance = {{custom_attribute.${current_rewards_balance}}} %}
{{custom_attribute.${giftcard_balance} | plus: balance}}
```

- [Full Math Filter Documentation from Shopify][19]

## String Filters

String filters are used to manipulate the outputs and variables of strings. For example, you can [capitalize][21] a string.

- [Full String Filter Documentation from Shopify][20]

## Array Filters

Array filters can be used to manipulate the outputs of arrays. All array filters listed [here][22] are compatible with Braze's message composer.

For example, the [first][23] filter can used to template the first value in an array into the message composer. Please note that the first value in an array custom attribute array is the most recently added value.

The filter `{{custom_attribute.${my_array} | first}}` will yield the most recently added value. If `my_array` is a custom attribute array that consists of "apple, orange, banana" the output of `{{custom_attribute.${my_array} | first}}` will be "apple".

- [Full Array Filter Documentation from Shopify][22]

## Date Filters

Date filters can be used to convert a timestamp into a different date format. Let's say that the value of `date_attribute` is the timestamp 2015-06-03 17:13:41 UTC.

The filter `{{custom_attribute.${date_attribute} | date: "%b","%d"}}` will reformat this timestamp to "03 June". There are many other date formatting options which you can find [here][24].

In addition to these formatting options, we also support converting a timestamp to Unix time with the ``%s` date filter.

For example, to get the `date_attribute` in Unix time, you'd input the following and get the integer `1433351621` as an output:

```liquid
{{custom_attribute.${date_attribute} | date: "%s"  }}
```

In addition to the filters that you'll find listed in Shopify's documentation, we also support the "time_zone" filter.

The "Time_zone" filter takes a time, a time zone, and a date format and returns the time in that timezone in the specified date format. For example, let's say that the value of `{{custom_attribute.${date_attribute}}}` is 2015-04-03 9:00:00 UTC.

**Input:**

```liquid
{{custom_attribute.${date_attribute} | time_zone: 'America/Los_Angeles' | date: '%a %b %e %T' }}
```

**Output:**

```
Fri April 3 2:00:00
```

Additionally you can access the current time for manipulation by doing something like the following:

```liquid
{{ 'now' | date: '%Y-%m-%d %H:%M:%S' }}
```

Which will return something like:

```liquid
2016-08-22 18:13:13
```

{% endraw %}

- [Full Date Filter Documentation from Shopify][24]

[1]: http://docs.shopify.com/themes/liquid-documentation/basics
[2]: {{ site.baseurl }}/user_guide/data_and_analytics/custom_data/custom_attributes/
[3]: http://docs.shopify.com/themes/liquid-documentation/filters
[5]: {{ site.baseurl }}/user_guide/data_and_analytics/custom_data/custom_attributes/#custom-attribute-data-types
[6]: #conditional-messaging-logic-tags
[7]: https://docs.shopify.com/themes/liquid-documentation/tags
[12]: https://docs.shopify.com/themes/liquid-documentation/filters
[4]: {% image_buster /assets/img_archive/personalized_firstname_.png %}
[8]: http://docs.shopify.com/themes/liquid-documentation/tags/control-flow-tags "Control Flow Tags"
[9]: #connected-content
[11]: {{ site.baseurl }}/user_guide/data_and_analytics/custom_data/custom_events/
[13]: {% image_buster /assets/img_archive/total_revenue_ios.png %}
[14]: {% image_buster /assets/img_archive/games_liquid.png %}
[15]: {% image_buster /assets/img_archive/liquid_abort.png %}
[16]: #manipulating-message-content-filters
[17]: #conditional-logic
[18]: #aborting-messages
[19]: https://docs.shopify.com/themes/liquid/filters/math-filters
[20]: https://docs.shopify.com/themes/liquid/filters/string-filters
[21]: https://docs.shopify.com/themes/liquid/filters/string-filters#capitalize
[22]: https://docs.shopify.com/themes/liquid/filters/array-filters
[23]: https://docs.shopify.com/themes/liquid/filters/array-filters#first
[24]: https://docs.shopify.com/themes/liquid/filters/additional-filters#date
[25]: https://docs.shopify.com/themes/liquid/basics/operators
[26]: {% image_buster /assets/img_archive/developer_console.png %}
[27]: #adding-personalizable-attributes-objects
[29]: {% image_buster /assets/img_archive/MemberSince.png %}
[30]: #variable-tags
[31]:https://docs.shopify.com/themes/liquid/tags/variable-tags
[32]:https://docs.shopify.com/themes/liquid/tags/iteration-tags
[33]:{% image_buster /assets/img_archive/SupportedAttributes.png %}
[34]:{% image_buster /assets/img_archive/personalized_iflogic_.png %}
[36]:{% image_buster /assets/img_archive/value_null.png %}
[37]:#accounting-for-null-attribute-values
[38]: {{ site.baseurl }}/user_guide/personalization_and_dynamic_content/connected_content/about_connected_content/
[39]: #most-recently-used-device-information
[40]: {{ site.baseurl }}/developer_guide/platform_integration_guides/ios/initial_sdk_setup/#optional-idfa-collection
[41]: https://dashboard-01.braze.com/app_settings/app_settings/custom_attributes
[42]: https://dashboard-01.braze.com/app_settings/app_settings/custom_events
[43]: {{ site.baseurl }}/user_guide/message_building_by_channel/email/managing_user_subscriptions/#managing-user-subscriptions
[44]: {% image_buster /assets/img_archive/insert_liquid_var_arrow.png %}
[45]: {% image_buster /assets/img_archive/insert_var_shot.png %}
[46]: #targeted-device-information