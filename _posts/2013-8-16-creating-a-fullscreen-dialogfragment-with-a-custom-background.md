---
layout: post
title:  "Creating a fullscreen DialogFragment with a custom background"
date:   2013-08-16 09:30:00
categories: android
tags: DialogFragment
---
The default Dialogs (or rather the [DialogFragments](http://developer.android.com/reference/android/app/DialogFragment.html)) look pretty good in Android since Honeycomb, but the default look-and-feel doesn't go well together with all app designs. Not to mention sometimes you need a fully different layout, a custom background color, or a semitransparent background with no grey dimming at all. We're talking about Android, where (almost) everything is possible, so there is a solution for this problem as well.
<!-- more -->

Customizing a DialogFragment is a quite easy task. Create a class extending DialogFragment, and override the _onCreateDialog_ method, which is responsible for creating the Dialog. Instantiate a Dialog, then make the following calls on the instance:

*   _requestWindowFeature(Window.FEATURE_NO_TITLE)_ - removes the dialog's title
*   _getWindow().setLayout(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT)_ forces the dialog to occupy the whole screen estate
*   _setBackgroundDrawable()_ - sets the dimming drawable. This can be transparent, a solid color, a gradient or a custom drawable (even an image)
*   create the layout and set it to the dialog using _setContentView()_

These four steps give you an empty fullscreen layout to play with, with the option to add a semi-transparent background, revealing the underlying activity. From this point, only your imagination is the limit; a good example of what can be achieved is the following screen, taken from an upcoming app: 

<p  align="center">
    <img src="http://localhost:4000/img/post/fullscreen_dialogfragment.png"/>
</p>

You can find more on using and customizing the DialogFragment at the [official developer site](http://developer.android.com/reference/android/app/DialogFragment.html). Here is a short sample code illustrating a DialogFragment with a yellow background and an empty layout.

{% highlight java %}
public class CustomDialogFragment extends DialogFragment {

    public CustomDialogFragment() {}

    @Override
    public Dialog onCreateDialog(final Bundle savedInstanceState) {

        // the content
        final RelativeLayout root = new RelativeLayout(getActivity());
        root.setLayoutParams(new ViewGroup.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT));

        // creating the fullscreen dialog
        final Dialog dialog = new Dialog(getActivity());
        dialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        dialog.setContentView(root);
        dialog.getWindow().setBackgroundDrawable(new ColorDrawable(Color.YELLOW));
        dialog.getWindow().setLayout(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT);

        return dialog;
    }

}
{% endhighlight %}