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
 * Version 1.5
 * September 2, 2009
 *
 * Copyright (c) 2008 - 2009 Bendik Rognlien Johansen
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
 *	 @Deprecated
 *   allowResize:(boolean) If true, the viewport can be resized.
 *               default: true
 *               NOT IMPLEMENTED

 *   allowResizeX:(boolean) If true, the viewport can be resized in the x direction.
 *               default: true
 *               
 *   allowResizeY:(boolean) If true, the viewport can be resized in the y direction.
 *               default: true
 *               
 *
 *
 *   imageMaxWidth: (number) The maximum width of the zoomed image.
 *             required: no
 *             default: 2000
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
 *   20090331: Added image as first argument
 *   callback: (function) A function that is called after the image has been panned or zoomed. 
 *             arguments: image, topX, topY, bottomX, bottomY
 *             required: no
 *
 *
 *   loading: (string) Path to an image that is shown while the main image loads.
 *             required: no
 *
 **/


;(function($) {

	// Default settings   
	var defaultSettings = {
		allowZoom: true
		,allowPan: true
		,allowResizeX: true
		,allowResizeY: true
		,zoomCursor: "crosshair"
		,panCursor: "move"
		,disabledCursor: "not-allowed"
		,viewportWidth: 400
		,viewportHeight: 300
		,viewportMinWidth: 100
		,viewportMinHeight: 80
		,viewportMaxWidth: 800
		,viewportMaxHeight: 800
		,imageMaxWidth: 2500
		,topX: -1
		,topY: -1
		,bottomX: -1 
		,bottomY: -1
		,callback: function(topX, topY, bottomX, bottomY) {}
	};
  
	
	function handleMouseMove(mmevt) {
		var image = $(this);
		var dim = image.data("dim");
		
		var clickX = (mmevt.pageX - $(this).offset({scroll: false}).left);
		var clickY = (mmevt.pageY - $(this).offset({scroll: false}).top);
		
		var cursors = {se:"se-resize", s:"s-resize", e:"e-resize"};
		
		var edge = getEdge(dim, clickX, clickY);
		if(edge) {
			dim.cursor = cursors[edge];
		}
		else {
			dim.cursor = dim.panCursor;
		}
		console.log("Edge " + edge);
		
		image.css("cursor", dim.cursor);
	}
	
	/**
	 * Find the edge n, e, s, w, 
	 */
	function getEdge(dim, x, y) {
		//var dim = image.data("dim");
		
		var scale = dim.width / dim.imageWidth;
		
		var fromEdgdeN = (y) - (dim.topY*scale);
		var fromEdgdeW = (x) - (dim.topX*scale);
		
		var fromEdgdeE = dim.viewportWidth - (x - (dim.topX*scale));
		var fromEdgdeS = dim.viewportHeight - (y - (dim.topY*scale));


		if(fromEdgdeE < 15 && fromEdgdeS < 15 && (dim.allowResizeX || dim.allowResizeY)) {
			return "se";
		}
		else if(fromEdgdeW < 15 && dim.allowResizeX) {
			return "w";
		}
		else if(fromEdgdeN < 15 && dim.allowResizeY) {
			return "n";
		}
		else if(fromEdgdeE < 15 && dim.allowResizeX) {
			return "e";
		}
		else if(fromEdgdeS < 15 && dim.allowResizeY) {
			return "s";
		}
	}
	
	function handleMouseOver(event) {
		var image = $(this);
		var dim = image.data("dim");
		image.css("cursor", dim.cursor);
		
		$(this).mousemove(handleMouseMove);
		
	}
	
	function handleMouseOut(event) {
		var image = $(this);
		var dim = image.data("dim");
		image.css("cursor", dim.cursor);
	}
	


	function handleMouseDown(mousedownEvent) {
		mousedownEvent.preventDefault();
		var image = $(this);
		var dim = image.data("dim");

		dim.origoX = mousedownEvent.clientX;
		dim.origoY = mousedownEvent.clientY;

		var clickX = (mousedownEvent.pageX - $(this).offset({scroll: false}).left);
		var clickY = (mousedownEvent.pageY - $(this).offset({scroll: false}).top);
		
		var edge = getEdge(dim, clickX, clickY);
		
		if(edge) {
			/*if(edge == "se") {*/
				$(document).mousemove(function(e) {
					image.handleViewPortResize(e);
				});
			/*}*/
		}
		else if(dim.allowZoom && (mousedownEvent.shiftKey || mousedownEvent.ctrlKey) ) {
			dim.cursor = dim.zoomCursor;
			image.css("cursor", dim.zoomCursor);
			$("body").css("cursor", dim.zoomCursor);
			$(document).mousemove(function(e) {
				image.zoom(e);
			});
		}
		else if(dim.allowPan) {
			dim.cursor = dim.panCursor;
			image.css("cursor", dim.panCursor);
			$("body").css("cursor", dim.panCursor);
			$(document).mousemove(function(e) {
				image.pan(e);
			});
		}

		$(document).mouseup(function() {
			var dim = image.data("dim");
			dim.cursor = dim.panCursor;
			$("body").css("cursor", "default");
			image.css("cursor", dim.cursor);
			$(this).unbind("mousemove").unbind("mouseup").unbind("mouseout");
			image.store();
		});
		return false;
	}

	$.fn.extend({
		setup: function() {
			var image = $(this);
			var dim = image.data("dim");
			dim.cursor = dim.panCursor; // Default cursor

			dim.width = dim.imageWidth;
			dim.height = dim.imageHeight;


			// If no coordinates are set, make sure the image size is not smaller than the viewport
			if(dim.topX < 0) {
				dim.topX = 0;
				dim.topY = 0;

				if((dim.imageWidth/dim.viewportWidth) > (dim.imageHeight/dim.viewportHeight)) {
					dim.bottomY = dim.imageHeight;
					dim.bottomX = dim.viewportWidth * (dim.imageHeight/dim.viewportHeight);
				}
				else {
					dim.bottomX = dim.imageWidth;
					dim.bottomY = dim.viewportHeight * (dim.imageWidth/dim.viewportWidth);
				}
			}




			var scaleX = dim.viewportWidth/(dim.bottomX - dim.topX);
			var scaleY = dim.viewportHeight/(dim.bottomY - dim.topY);

			dim.width = dim.width * scaleX;
			dim.height = dim.height * scaleY;

			dim.oldWidth = dim.width;
			dim.oldHeight = dim.height;

			dim.x = -(dim.topX * scaleX);
			dim.y = -(dim.topY * scaleY);

			image.resize();
			image.store();

			image.css({
				position: "relative"
				,display: "block"
			});

			
	    if(dim.allowPan || dim.allowZoom) {
	    	image.mousedown(handleMouseDown);
	    	image.mouseover(handleMouseOver);
	    	image.mouseout(handleMouseOut);
	    }
	    else {
	    	image.css("cursor", dim.disabledCursor);
	    	image.mousedown(function(e) {
	    		e.preventDefault();
	    	});
	    }

		}

		,imagetool: function(settings) {
			return this.each(function() {
				var image = $(this).css({display: "none"});


				// Add settings to each image object
				var dim = $.extend({}, defaultSettings, settings);
				image.data("dim", dim);

				// Set up the viewport        
				var viewportCss = {
					backgroundColor: "#fff"
					,position: "relative"
					,overflow: "hidden"
					,width: dim.viewportWidth + "px"
					,height: dim.viewportHeight + "px"
				};
				var viewportElement = $("<div class=\"viewport\"><\/div>");
				viewportElement.css(viewportCss);

				image.wrap(viewportElement);
				
				$(this).setup();				
				
			}); // end this.each
		} // End imagetool()
		
		,store: function() {
		var image = $(this);
		var dim = image.data("dim");

		var scale = dim.width / dim.imageWidth;      

		dim.topX = (-dim.x) / scale;
		dim.topY = (-dim.y)  / scale;

		dim.bottomX = dim.topX + (dim.viewportWidth / scale);
		dim.bottomY = dim.topY + (dim.viewportHeight / scale);

		if(typeof dim.callback == 'function') {
			dim.callback(image, parseInt(dim.topX), parseInt(dim.topY), parseInt(dim.bottomX), parseInt(dim.bottomY));
		}
		return image;
	}

		/**
		 * Handles resize of the viewport
		 */
	,handleViewPortResize: function(e) {
		e.preventDefault();
		var image = $(this);
		var dim = image.data("dim");
		
		var deltaX = dim.origoX - e.clientX;
		var deltaY = dim.origoY - e.clientY;

		dim.origoX = e.clientX;
		dim.origoY = e.clientY;

		var targetWidth = dim.viewportWidth - deltaX;
		var targetHeight = dim.viewportHeight - deltaY;

		if(targetWidth > dim.viewportMaxWidth) {
			dim.viewportWidth = dim.viewportMaxWidth;
		}
		else if(targetWidth < dim.viewportMinWidth) {
			dim.viewportWidth = dim.viewportMinWidth;
		}
		else if(dim.allowResizeX) {
			dim.viewportWidth = targetWidth;
		}
		
		if(targetHeight > dim.viewportMaxHeight) {
			dim.viewportHeight = dim.viewportMaxHeight;
		}
		else if(targetHeight < dim.viewportMinHeight) {
			dim.viewportHeight = dim.viewportMinHeight;
		}
		else if(dim.allowResizeY) {
			dim.viewportHeight = targetHeight;
		}
		

		image.parent().css({
			width: dim.viewportWidth + "px"
			,height: dim.viewportHeight + "px"
		});
		
		image.fit(e);
		
	}
	
	,viewportResize: function(width, height) {
		var image = $(this);
		var dim = image.data("dim");
		dim.viewportWidth = width;
		dim.viewportHeight = height;
		image.parent().css({
			width: dim.viewportWidth + "px"
			,height: dim.viewportHeight + "px"
		});
		
		image.fit();
	}
	
	
	,fit: function() {
		var image = $(this);
		var dim = image.data("dim");
		if(dim.viewportWidth > dim.width || dim.viewportHeight > dim.height) {
			var factor = dim.viewportWidth / dim.width;
			dim.width = dim.viewportWidth;
			dim.height = dim.height * factor;
			if(image.resize()) {
				//dim.origoY = e.clientY;
			}
		}
		/*
		var cx = dim.width /(-dim.x + (dim.viewportWidth/2));
		var cy = dim.height /(-dim.y + (dim.viewportHeight/2));


		dim.x = dim.x - ((dim.width - dim.oldWidth) / cx);
		dim.y = dim.y - ((dim.height - dim.oldHeight) / cy);
		*/
		$(this).move();
	}

	/**
	 * Makes sure the images hugs the edges of the viewport.
	 */
	,hug: function() {
		var image = $(this);
		var dim = image.data("dim");
		
		
		
		var cx = dim.width /(-dim.x + (dim.viewportWidth/2));
		var cy = dim.height /(-dim.y + (dim.viewportHeight/2));


		dim.x = dim.x - ((dim.width - dim.oldWidth) / cx);
		dim.y = dim.y - ((dim.height - dim.oldHeight) / cy);

		$(this).move();
	}
	,zoom: function(e) {
		e.preventDefault();
		var image = $(this);
		var dim = image.data("dim");

		var factor = ( dim.origoY - e.clientY);

		dim.oldWidth = dim.width;
		dim.oldHeight = dim.height;

		dim.width = ((factor/100) * dim.width) + dim.width;
		dim.height = ((factor/100) * dim.height) + dim.height;

		if(image.resize()) {
			dim.origoY = e.clientY;
		}
	}

	,pan: function(e) {
		e.preventDefault();
		var image = $(this);
		var dim = image.data("dim");

		var deltaX = dim.origoX - e.clientX;
		var deltaY = dim.origoY - e.clientY;

		dim.origoX = e.clientX;
		dim.origoY = e.clientY;

		var targetX = dim.x - deltaX;
		var targetY = dim.y - deltaY;

		var minX = -dim.width + dim.viewportWidth;
		var minY = -dim.height + dim.viewportHeight;

		dim.x = targetX;
		dim.y = targetY;
		image.move();
	} // end pan



	,move: function() {
		var image = $(this);
		var dim = image.data("dim");
		var minX = -dim.width + dim.viewportWidth;
		var minY = -dim.height + dim.viewportHeight;

		if(dim.x > 0) {
			dim.x = 0;
		}
		else if(dim.x < minX) {
			dim.x = minX;
		}

		if(dim.y > 0) {
			dim.y = 0;
		}    
		else if(dim.y < minY) {
			dim.y = minY;
		}


		$(this).css({
			left: dim.x + "px"
			,top: dim.y + "px"
		});
		return image;
	}





	,resize: function() {
		var image = $(this);
		var dim = image.data("dim");
		// When attempting to scale the image below the minimum, set the size to minimum
		var wasResized = true;
		if(dim.width < dim.viewportWidth) {
			dim.height = parseInt(dim.imageHeight * (dim.viewportWidth/dim.imageWidth));
			dim.width = dim.viewportWidth;
			wasResized = false;

		}

		if(dim.height < dim.viewportHeight) {
			dim.width = parseInt(dim.imageWidth * (dim.viewportHeight/dim.imageHeight));
			dim.height = dim.viewportHeight;
			wasResized = false;
		}


		if(dim.width > dim.imageMaxWidth) {
			dim.height = parseInt(dim.height * (dim.imageMaxWidth/dim.width));
			dim.width = dim.imageMaxWidth;
			wasResized = false;
		}


		$(this).css({
			width: dim.width + "px"
			,height: dim.height + "px"
		});


		// Scale at center of viewport
		var cx = dim.width /(-dim.x + (dim.viewportWidth/2));
		var cy = dim.height /(-dim.y + (dim.viewportHeight/2));


		dim.x = dim.x - ((dim.width - dim.oldWidth) / cx);
		dim.y = dim.y - ((dim.height - dim.oldHeight) / cy);

		$(this).move();
		return wasResized;
	}
	});
})(jQuery); 