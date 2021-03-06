**********************************************************
How To: Display Shipping Method Icons on the Checkout Page
**********************************************************

.. warning::

    The changes suggested in this article may conflict with the add-ons that use the same hook.

=================
Override the Hook
=================

1. In the root directory of your CS-Cart installation, create the following directory path: */design/themes/[CURRENT_THEME_NAME]/templates/addons/my_changes/hooks/checkout*.

.. important ::

    Make sure that the **My changes** add-on is installed and activated in the **Add-ons → Manage add-ons** section of the Administration panel.

2. In this directory create the **shipping_method.override.tpl** file with the following content:

.. code-block:: html+smarty

    {if $display == "radio"}
        {assign var="shipping_image_pair" value=$shipping.shipping_id|fn_get_image_pairs:shipping:M}

        <p class="ty-shipping-options__method">
            <input type="radio" class="ty-valign" id="sh_{$group_key}_{$shipping.shipping_id}" name="shipping_ids[{$group_key}]" value="{$shipping.shipping_id}" onclick="fn_calculate_total_shipping_cost();" {$checked} />
            <label for="sh_{$group_key}_{$shipping.shipping_id}" class="ty-valign">
                {if $shipping_image_pair}
                    {include file="common/image.tpl" obj_id=$shipping_id images=$shipping_image_pair image_width=100 image_height=100}
                {/if}
                {$shipping.shipping} {$delivery_time} - {$rate nofilter}
            </label>
        </p>

    {elseif $display == "select"}
        <option value="{$shipping.shipping_id}" {$selected}>{$shipping.shipping} {$delivery_time} - {$rate nofilter}</option>

    {elseif $display == "show"}
        <p>
            {$strong_begin}{$rate.name} {$delivery_time} - {$rate nofilter}{$strong_begin}
        </p>
    {/if}

In this example the shipping method icon width is 100 and its height is 100 too. If you want to define another width and height, type your own numbers for the ``image_width`` and ``image_height`` parameters.

.. note ::

    If your changes to the template are not displayed, try :doc:`clearing the template cache <../../../developer_guide/getting_started/cache_clearing>`.

-------------------------------------------
Additional Changes for CS-Cart 4.3.6 and Up
-------------------------------------------

If you use CS-Cart 4.3.6 and up, make the changes described above. Then add the following code to the end of **shipping_method.override.tpl**:

.. code-block:: html+smarty

    {if $shipping.description}
        <div class="ty-checkout__shipping-tips">
            <p>{$shipping.description nofilter}</p>
        </div>
    {/if}

--------------------------------
Compatibility with Other Add-ons
--------------------------------

If an add-on doesn't work properly after you apply these changes, it may be because it uses the same hook. In that case try to find **shipping_method.post.tpl** in */design/themes/[CURRENT_THEME_NAME]/templates/addons/[addon_name]/hooks/checkout*. If you find that file, copy its content to the end of the same file of the **My changes** add-on.

============================
Upload Shipping Method Icons
============================

1. In the Administration panel of your store, go to **Administration → Shipping & Taxes → Shipping Methods**, click the gear button of the necessary shipping method, and choose **Edit**.

2. Upload an image in the **Icon** section in the opened page.

3. Click **Save and close**.
