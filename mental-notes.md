# **Mental Notes + Questions**

#### **11/8/2018**

How can I check the hex value of a background and if it is 50% to dark, add the CSS value of " filter: invert(1); " to the image?

[JavaScript Color Contraster](https://stackoverflow.com/questions/5650924/javascript-color-contraster#)

    var darkOrLight = function(red, green, blue) {
    var brightness;
    brightness = (red * 299) + (green * 587) + (blue * 114);
    brightness = brightness / 255000;

    // values range from 0 to 1
    // anything greater than 0.5 should be bright enough for dark text
    if (brightness >= 0.5) {
        return "dark-text";
    } else {
        return "light-text";
    }
    }

[Calculating Color Contrast](https://24ways.org/2010/calculating-color-contrast/)

    function getContrast50(hexcolor){
        return (parseInt(hexcolor, 16) > 0xffffff/2) ? 'black':'white';
    }






    function getContrast50(hexcolor){
     return (parseInt(hexcolor.replace('#', ''), 16) > 0xffffff/4) ? 'black':'white';
    }