---
layout: post
title: "Putting a UISwitch in a UITableViewCell"
date: 2013-01-10 17:43
comments: false
categories:
---

Due to the ubiquitousness    of using UITableViews to display various types of UIs in iOS, many different types of content end up inside UITableViewCells.  One common approach involves putting a `UISwitch` inside a table cell to represent an on/off setting:

<!-- more -->
<div class="screenshots centered">
    {% img http://pictures.seancarpenter.net/blog/uiswitch_cell.png 'UISwitch in a UITableViewCell' 'UISwitch in a UITableViewCell' %}
    <em>A UISwitch in a UITableViewCell</em>
</div>

So how do you actually go about making this type of cell?  The method I'm going to describe here doesn't require a custom UITableViewCell subclass so that makes it easy to use in just about any iOS project.

## Configuring a cell to display a UISwitch
The first step is to configure your table cell to contain a `UISwitch` and a `UILabel` (for the description):
{% codeblock lang:objc %}
-(UITableViewCell*) tableView:(UITableView*) tableView cellForRowAtIndexPath:(NSIndexPath*) indexPath {
    static NSString* cellIdentifier = @"switchCell";
    UITableViewCell* cell = [tableView dequeueReusableCellWithIdentifier:cellIdentifier];
    if (cell == nil) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:cellIdentifier];
        UISwitch* s = [[UISwitch alloc] init];
        CGSize switchSize = [s sizeThatFits:CGSizeZero];
        s.frame = CGRectMake(cell.contentView.bounds.size.width - switchSize.width - 5.0f,
                             (cell.contentView.bounds.size.height - switchSize.height) / 2.0f,
                             switchSize.width,
                             switchSize.height);
        s.autoresizingMask = UIViewAutoresizingFlexibleLeftMargin;
        s.tag = 100;
        [s addTarget:self action:@selector(switchChanged:) forControlEvents:UIControlEventValueChanged];
        [cell.contentView addSubview:s];

        UILabel* l = [[UILabel alloc] init];
        l.text = @"Notifications";
        CGRect labelFrame = CGRectInset(cell.contentView.bounds, 10.0f, 8.0f);
        labelFrame.size.width = cell.contentView.bounds.size.width / 2.0f;
        l.font = [UIFont boldSystemFontOfSize:17.0f];
        l.frame = labelFrame;
        l.backgroundColor = [UIColor clearColor];
        cell.accessibilityLabel = "Notifications";
        [cell.contentView addSubview:l];
    }
    ((UISwitch*)[cell.contentView viewWithTag:100]).on = value; // "value" is whatever the switch should be set to
    return cell;
}
{% endcodeblock %}

This looks like a lot of code, but most of it is related to positioning the label and the switch in the table cell.

First, the switch.  On line 7, we get the size needed to display the switch.  Then the switch's frame can be calculated (lines 8 - 11).  The `x` position of the switch is calculated by subtracting the switch's width (plus a little padding) from the cell's `contentView` width.  This places the switch against the right-hand side of the cell (inset by the padding amount).  The `y` position of the switch is calculated to center the switch vertically in the cell.

The label is a little simpler.  On line 19 `CGRectInset` is used to give us a `CGRect` with a horizontal padding of 10 and a vertical padding of 8.  Then the width of the label is shrunk on line 20 to be half the width of the cell.

Once the cell is configured, we set the switch's value (finding it via the tag assigned on line 13) and we're off to the races.

## Responding to the switch being switched
Now we have a cell that displays a label and a switch with the switch's value set appropriately.  How do we handle the user tapping the switch?  On line 14 in the setup code above, we added an action to be called when the switch's value is changed.  We need to implement that action method:

{% codeblock lang:objc %}
-(void) switchChanged:(id)sender {
    UISwitch* switcher = (UISwitch*)sender;
    BOOL value = switcher.on;
    // Store the value and/or respond appropriately
}
{% endcodeblock %}

## Rounding out the behavior
There's one additional step we can take to improve the cell's behavior.  Right now, if the user taps anywhere in the cell that isn't the switch, nothing happens.  We should make it so that tapping the cell anywhere (the label, the padding, etc.) triggers the switch.  That can be handled in the table view's delegate:

{% codeblock lang:objc %}
-(void) tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
    [tableView deselectRowAtIndexPath:indexPath animated:NO];
    UITableViewCell* cell = [tableView cellForRowAtIndexPath:indexPath];
    UISwitch* switcher = (UISwitch*)[cell.contentView viewWithTag:100];
    [switcher setOn:!switcher.on animated:YES];
    [self switchChanged:switcher];
}
{% endcodeblock %}

First we get a reference to the cell that was tapped, then the switch in that cell.  Line 5 animates toggling of the switch's value.  Then we call the same action method that was assigned to the switch to process the value.

## Wrapping up
There are other ways to include a `UISwitch` in a `UITableViewCell`.  One method is to subclass `UITableViewCell` and implement similar logic to that shown here.  That method is useful for encapsulating the switch and label related code.  If you take that route, you will probably want to create a delegate for your new subclass to allow the consuming code to be notified of switch value changes.
