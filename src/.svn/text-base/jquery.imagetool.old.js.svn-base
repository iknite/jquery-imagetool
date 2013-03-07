/**
 * $Date$
 * 
 * Revision: $Revision$
 * 
 * 
 * Imagetool
 * 
 * Imagetool is a simple plugin for jQuery providing basic cropping and scaling capabilities for images.
 *
 * It works by wrapping the selected image in a <div> (the viewport) and manipulates css properties based on user input.
 *
 * Tested in Safari 3, Firefox 2, MSIE 6, MSIE 7
 * 
 * Version 1.0
 * August 8, 2008
 *
 * Copyright (c) 2008 Bendik Rognlien Johansen
 * 
 * @desc Adds editing capabilities to image (<img>) elements
 * @author Bendik Rognlien Johansen
 * @version 1.0
 *
 * @name Imagetools
 * @type jQuery
 *
 * @cat plugins/Media
 * 
 * @example $('img').imagetool({options});
 * @desc Add editing capabilities to image (<img>) elements
 * @options
 *
 *   allowZoom:  (boolean) If true, the image is zoomable.
 *               default: true
 *
 *   allowPan:   (boolean) If true, the image can be panned.
 *               default: true
 *
 *
 *
 *   imageWidth: (number) The actual width (pixels) of the image used.
 *               required: yes
 *
 *   imageHeight: (number) The actual height (pixels) of the image used.
 *               required: yes
 *
 *
 *
 *   viewportWidth: (number) The width (pixels) of the viewport.
 *                  required: yes
 *
 *   viewportHeight: (number) The height (pixels) of the viewport.
 *                   required: yes
 *
 *
 *
 *   topX: (number)
 *         required: no
 *         default: 0
 *
 *   topY: (number)
 *         required: no
 *         default: 0
 *
 *   bottomX: (number)
 *            required: no
 *            default: 0
 *
 *   bottomY: (number)
 *            required: no
 *            default: 0
 *
 *   
 *   callback: (function) A function that is called after the image has been panned or zoomed. 
 *             arguments: topX, topY, bottomX, bottomY
 *             required: no
 *
**/


;(function($) {

  /*
  Default settings 
  */
  var defaultSettings = {
    allowZoom: true
   ,allowPan: true
   ,imageWidth: 450
   ,imageHeight: 300
   ,viewportWidth: 320
   ,viewportHeight: 180
   ,imageMaxWidth: 2000
   ,topX: -1
   ,topY: -1
   ,bottomX: -1 
   ,bottomY: -1
   ,callback: function(topX, topY, bottomX, bottomY) {}
  };



  $.fn.extend({

    store: function(settings) {
      var scale = settings.width / settings.imageWidth;      
      
      var dim = {};
      dim.topX = (-settings.x) / scale;
      dim.topY = (-settings.y)  / scale;
        
      dim.bottomX = dim.topX + (settings.viewportWidth / scale);
      dim.bottomY = dim.topY + (settings.viewportHeight / scale);

      if(typeof settings.callback == 'function') {
        settings.callback(parseInt(dim.topX), parseInt(dim.topY), parseInt(dim.bottomX), parseInt(dim.bottomY));
      }      
    }
    
    
    
    
   ,move: function(settings) {    
      var minX = -settings.width + settings.viewportWidth;
      var minY = -settings.height + settings.viewportHeight;
      
      if(settings.x > 0) {
        settings.x = 0;
      }
      else if(settings.x < minX) {
         settings.x = minX;
      }
          
       if(settings.y > 0) {
         settings.y = 0;
       }    
       else if(settings.y < minY) {
         settings.y = minY;
       }
      
       
       $(this).css({
         left: settings.x + "px"
        ,top: settings.y + "px"
       });
     }
    
    
    
    
    
      ,resize: function(settings) {
       // When attempting to scale the image below the minimum, set the size to minimum
       var wasResized = true;
       if(settings.width < settings.viewportWidth) {
         settings.height = parseInt(settings.imageHeight * (settings.viewportWidth/settings.imageWidth));
         settings.width = settings.viewportWidth;
         wasResized = false;
         
       }
       
       if(settings.height < settings.viewportHeight) {
         settings.width = parseInt(settings.imageWidth * (settings.viewportHeight/settings.imageHeight));
         settings.height = settings.viewportHeight;
         wasResized = false;
       }
       
       
      if(settings.width > settings.imageMaxWidth) {
         settings.height = parseInt(settings.height * (settings.imageMaxWidth/settings.width));
         settings.width = settings.imageMaxWidth;
         wasResized = false;
       }
      
       
       $(this).css({
         width: settings.width + "px"
        ,height: settings.height + "px"
       });
       
       
       // Scale at center of viewport
       var cx = settings.width /(-settings.x + (settings.viewportWidth/2));
       var cy = settings.height /(-settings.y + (settings.viewportHeight/2));
       
     
       settings.x = settings.x - ((settings.width - settings.oldWidth) / cx);
       settings.y = settings.y - ((settings.height - settings.oldHeight) / cy);

       $(this).move(settings);
       return wasResized;
     }
    

    
    ,imagetool: function(settings) {
      settings = $.extend({}, defaultSettings, settings);
      
      
      
      
      
      return this.each(function() {  
        var image = $(this);
        settings.width = settings.imageWidth;
        settings.height = settings.imageHeight;
        
        /*
        If no coordinates are set, make sure the image size is not smaller than the viewport
        */
       if(settings.topX < 0) {
         settings.topX = 0;
         settings.topY = 0;
         
         if((settings.imageWidth/settings.viewportWidth) > (settings.imageHeight/settings.viewportHeight)) {
           settings.bottomY = settings.imageHeight;
           settings.bottomX = settings.viewportWidth * (settings.imageHeight/settings.viewportHeight);
         }
         else {
           settings.bottomX= settings.imageWidth;
           settings.bottomY = settings.viewportHeight * (settings.imageWidth/settings.viewportWidth);
         }
       }
       
       
       
       
       var scaleX = settings.viewportWidth/(settings.bottomX - settings.topX);
       var scaleY = settings.viewportHeight/(settings.bottomY - settings.topY);
        
       settings.width = settings.width * scaleX;
       settings.height = settings.height * scaleY;
       
       settings.oldWidth = settings.width;
       settings.oldHeight = settings.height;
        
       settings.x = -(settings.topX * scaleX);
       settings.y = -(settings.topY * scaleY);
        

        
        $(this).store(settings);
        

      
        /*
        Set up the viewport
        */
        var viewportCss = {
          backgroundColor: "#fff"
         ,position: "relative"
         ,overflow: "hidden"
         ,width: settings.viewportWidth + "px"
         ,height: settings.viewportHeight + "px"
         ,border: "1px solid #333"
        };
        var viewportElement = $("<div><\/div>");
        viewportElement.css(viewportCss);

        image.wrap(viewportElement);

        
                
        
        image.resize(settings);
        
        image.css({
          position: "relative"
         ,cursor: "move"
         ,display: "block"
        });
        
        
        
        
        image.mouseup(function() {
          $(this).unbind( "mousemove" );
          $(this).store(settings);
        });
        
        
        // When leaving the the element, cancel pan/scale
        
        image.mouseout(function() {
          $(this).unbind( "mousemove" );
          $(this).store(settings);
        });
        
      
      
      image.mousedown(function(mousedownEvent) {
        mousedownEvent.preventDefault();
        settings.origoX = mousedownEvent.clientX;
        settings.origoY = mousedownEvent.clientY;
        
        var clickX = (mousedownEvent.pageX - $(this).offset({scroll: false}).left);
        var clickY = (mousedownEvent.pageY - $(this).offset({scroll: false}).top);
        
        
        if(settings.allowZoom && (mousedownEvent.shiftKey || mousedownEvent.ctrlKey) ) {
          image.mousemove(zoom);
        }
        else if(settings.allowPan) {
          image.mousemove(pan);
        }


        function zoom(e) {
          e.preventDefault();
          var image = $(this);
          
          var factor = ( settings.origoY - e.clientY);
          
          settings.oldWidth = settings.width;
          settings.oldHeight = settings.height;
          
          settings.width = ((factor/100) * settings.width) + settings.width;
          settings.height = ((factor/100) * settings.height) + settings.height;
          
          if($(this).resize(settings)) {
            settings.origoY = e.clientY;
          }
        } // end scale
           
              
              
        
        
        
        
        function pan(e) {
          e.preventDefault();
          var image = $(this);
          var deltaX = settings.origoX - e.clientX;
          var deltaY = settings.origoY - e.clientY;
            
          settings.origoX = e.clientX;
          settings.origoY = e.clientY;
          
          var targetX = settings.x - deltaX;
          var targetY = settings.y - deltaY;
          
          var minX = -settings.width + settings.viewportWidth;
          var minY = -settings.height + settings.viewportHeight;
          
          settings.x = targetX;
          settings.y = targetY;
          image.move(settings);
        } // end pan

        
        
        return false;
      });

      }); // end this.each
    }

  });
})(jQuery); 