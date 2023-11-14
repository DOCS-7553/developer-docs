+++
prev = "/advanced/widgets/"
title = "Admin Area"
weight = 70

+++

The admin area of WHMCS can be customised through the use of themes and hooks.

## Getting Started

The default template that ships with WHMCS is called the Blend theme.

If you wish to make changes, we recommend first creating a copy of the theme with a custom name. This ensures your customisations can be preserved when applying future updates.

Make a copy of the Blend theme directory.

* Copy the `~/admin/templates/blend/` directory to `~/admin/templates/yourname/`

If the admin directory name has been customised, adjust the path accordingly.

{{% notice info %}}
Theme names should be a single word, consisting of only lowercase letters and numbers.
{{% /notice %}}

Now it’s time to customise your theme.

## Customising

The header and footer template files that are common to every page and act as a wrapper around the primary body content are a good place to start for this.

* We strongly recommend you maintain all of the output variables present in the default template files in your custom header/footer as this will help ensure compatibility with addons and extensions you later install

* The footer template file includes a number of lines of code which are essential to the correct operation of the admin area, particularly the client limit notifications. admin user notes, and the intelligent search option on each page. Please take care not to remove these lines.

### Logo

In order to make changes to the logo displayed on the top left corner of admin area pages, replace the `logo.gif` file in the `~/admin/templates/yourname/images` folder.

## CSS Styling

In order to make changes to any of the CSS that is applied by default, please create a custom CSS file in your theme and load it using the header template file.

## Templates

All admin themes contain the following template files:

* authconfirm.tpl - This template file is used to display the Password Confirmation prompt on any admin area page that requires it. For example: *Setup > General Settings*.

* clientssummary.tpl - Controls the display of the Summary Tab and associated information within an individual Clients Profile.

* footer.tpl - This template file controls the footer of each admin area page.

* header.tpl - This template file controls the header of each admin area page.

* homepage.tpl - Controls the display of the Admin Dashboard. Widgets are the building blocks of the admin dashboard and are the recommended way to add additional data and information. We have documentation on creating and working with widgets available at [Widgets](https://developers.whmcs.com/advanced/widgets/).

* nav.tpl - Controls the primary navigation dropdown menu bar.

* sidebar.tpl - This template file controls the left sidebar menu of each admin area page.

* systemhealthandupdates.tpl - Controls the display and styling of the System Health Status page.

* viewticket.tpl - Controls the display of the view for an individual support ticket when viewed within the admin area.

* viewticketcustomfields.tpl - Controls the display of custom fields and their data when viewing an individual support ticket within the admin area.

## Testing

When you are ready with your new template and wish to test it, follow the steps below:

* Login to the Admin Area

* Navigate to *My Account* using the menu located at the top of any page

* Under the Template setting, select the name of the template you created above

* Hit *Save Changes* and you will immediately begin seeing your new template

## Making your template live

To apply the change to other admin users, please navigate to *Setup > Staff Management > Administrators* and edit the Template setting for the applicable admin users.

## Translating

### Adding a Language

The admin language system in WHMCS allows you to create your own additional admin language translations.

We recommend starting by duplicating one of the existing admin language files.

{{% notice info %}}
Admin language file names should be a single word, consisting of only lowercase letters and numbers. The file must end with the extension ```.php```.
{{% /notice %}}

To do this:

1. Open an existing admin language file, for example `~/admin/lang/english.php`
2. Save this file with a new name. The name you choose will display in the language selection menu when adding or editing an admin in WHMCS.
3. Translate the words and phrases contained in it into your new language. Be careful not to change the language key names, only the words and phrases in the double quotes on each line.

{{% notice tip %}}
Be careful not to delete any of the quotation marks (") around the text strings or the semicolons on the ends of each line (;). If you want to use a quote character (") within your translated text, you must escape it. For example: ```\"```. The language files are written in PHP syntax so valid PHP code must be maintained.
{{% /notice %}}

4. Upload your new admin language file to your WHMCS installation's `~/admin/lang/` directory.

When you are ready with your new admin language and wish to test it, follow the steps below:

1. Log in to the Admin Area

2. Navigate to *My Account* using the menu at the top of any page.

3. Under the Language setting, select the name of the language you created above.

4. Click *Save Changes* and you will immediately begin seeing your new language.

### Language Overrides

We provide unencoded admin language files to allow you to view the language strings that WHMCS uses in the Admin Area.

We do not recommend editing these files directly. Instead, use override files.

Admin language file overrides allow you to customize the admin language strings and phrases in a way that the upgrade process preserves safely.

To customize admin language strings using overrides:

1. Create an `overrides` directory within the `~/admin/lang/` directory.
2. Create or copy the admin language file you want to override. For example, to create an override file for the English language, create the `~/admin/lang/overrides/english.php` file.
3. Open the newly-created file in your preferred editor.
4. Start the file with a <?php tag to indicate that this is a PHP file.
5. Enter the variable(s) you wish to override. For example, if you wanted to change a custom ticket status, you would add the following content to the file, where ```$statusname``` is the name of the custom ticket status that you configured under **Configuration > System Settings > Ticket Statuses.** and **Status Name** is the value to display on the support ticket pages in the admin area, such as **Support > Support Tickets** :


`~/admin/lang/overrides/english.php`
```
$_ADMINLANG['supportticketsstatus']['$statusname'] = "Status Name";
```

6. For each ticket status that you wish you change, repeat step #5. For example, a completed overrides file should look something like this:

`~/admin/lang/overrides/english.php`
```
<?php
$_ADMINLANG['supportticketsstatus']['teststatus'] = "Test Status";
$_ADMINLANG['supportticketsstatus']['senttobilling'] = "Sent To Billing";
$_ADMINLANG['supportticketsstatus']['contactvendor'] = "Contact Vendor"; 
```

7. Save the file.

## Further Customisation via Hooks

There are a number of hook points that can be used to introduce customisations. For a full list and the latest documentation, please visit our [Admin Area Hook Reference](https://developers.whmcs.com/hooks-reference/admin-area/)

Some example hook use cases are provided below.

{{% notice info %}}
If you wish to output custom javascript, we recommend using the AdminAreaFooterOutput hook point for best performance.
{{% /notice %}}

### Adding a link to the Actions section of the Client Summary page
```
<?php
/**
 * Provide a link to an addon module from the client summary page.
 */

if (!defined('WHMCS')) {
    die('This hook should not be run directly');
}

add_hook('AdminAreaClientSummaryActionLinks', 1, function ($vars) {
    $userid = $vars['userid'];

    $url = 'addonmodules.php?module=addon_module&userid=' . $userid;

    return [
        '<a href="' . $url . '">My custom addon link</a>',
    ];
});
```

### Adding a link to View Ticket page within the admin area

Displays a banner on the View Ticket page within the admin area that can be used to provide additional information. In this example, it includes the unique URL needed to view the current ticket within the client area.

```
<?php
/**
 * Display a banner on the admin area view ticket page.
 */

use WHMCS\Config\Setting;

if (!defined('WHMCS')) {
    die('This hook should not be run directly');
}

add_hook('AdminAreaViewTicketPage', 1, function ($vars) {
    $systemUrl = Setting::getValue('SystemURL');

    $ticketData = localAPI('GetTicket', array(
        'ticketid' => $vars['ticketid']
    ));

    $clientTicketUrl = $systemUrl . '/viewticket.php?tid='
        . $ticketData['tid'] . '&c=' . $ticketData['c'];

    return <<<EOT
<script>
$(document).ready(function() {
    $('<div class="alert alert-info text-center"><a href="' . $clientTicketUrl . '" target="_blank">' . $clientTicketUrl . '</a></div>').prependTo('#contentarea');
});
</script>
EOT;
});
```

### Manipulating the dom to change product behaviour

The below example demonstrates how you can use hooks and javascript to modify the behaviour of the admin area. In this specific example, we demonstrate how to disable the send welcome email option by default when creating a new client via the admin area.

```
<?php
/**
 * Disable the send welcome email checkbox by default when creating a new client.
 */

if (!defined('WHMCS')) {
    die('This hook should not be run directly');
}

add_hook('AdminAreaFooterOutput', 1, function ($vars) {
    if (strpos($_SERVER['REQUEST_URI'], 'clientsadd.php') !== false) {
        return '<script>
        $(document).ready(function() {
            $("input[name=sendemail]").attr("checked", false);
        });
    </script>';
    }
});
```
