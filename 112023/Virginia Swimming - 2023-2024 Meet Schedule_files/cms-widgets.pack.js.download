
if (window && !window.__currentScriptPath) window.__currentScriptPath = null;


if (window) window.__currentScriptPath = "/cms/scripts/CMSCommon.js";

var CMSCommon = {
    loadTeamLocale: function (url) {
    },
    getString: function(key, defaultValue) {
        if (!window.LOCALE_RAW_DATA) return defaultValue || key;
        return window.LOCALE_RAW_DATA[key] || defaultValue || key;
    },
    getMomentFormat: function(key) {
        var javaFormat = this.getString(key);
        return FormatUtil.javaToMomentFormat(javaFormat);

    },
    DateFormatter: {
        _format: function (date, initialFormat) {

        },
        long: function (date) {

        }
    },
    checkValidHtml: function(html, onCoolCallback) {
        $cmsService.getInvalidHtmlTags(html, function(tags) {
            if (tags) {
                var warningDialog = new HTMLWarningDialog(tags).callback(function (rs) {
                    if (rs && onCoolCallback) {
                        onCoolCallback();
                    }
                });
                warningDialog.open();
            } else {
                if (onCoolCallback) onCoolCallback();
            }
        });
    },
    em: function em(el) {
        if (!el) return Util.em();
        var size = parseFloat(window.getComputedStyle(el).fontSize);
        return size;
    }
};

CMSCommon.context = typeof(ADMIN_CONTEXT) != "undefined" ? ADMIN_CONTEXT :
	typeof(CONTEXT) != "undefined" ? CONTEXT : {localeUrl: ""};


if (window) window.__currentScriptPath = "/cms/scripts/analytics.js";

function sendAnalyticEvent(event, callback) {
    if (typeof(event) == "undefined") return;
    var thiz = this;
    var teamAlias = (typeof(TEAM_INFO) != "undefined" && TEAM_INFO.alias)
                 || (typeof(CONTEXT) != "undefined" && CONTEXT.teamAlias);
    var sendImpl = function(data) {
        ga(function() {
          // Logs an array of all tracker objects.
            var trackers = ga.getAll();
            if (!trackers || trackers.length == 0) {
                console.log("No trackers found to send ", event);
                if (callback) callback();
                return;
            }
            console.log("Start send tracking events....");
            for (var i = 0; i < trackers.length; i++) {
                var tracker = trackers[i];
                var name = tracker.get('name');
                if (name != "tuTracker") {
                    continue;
                }
                tracker.set("eventAction", data.action || "NoAction");
                tracker.set("eventCategory", data.category || "NoCategory");
                tracker.set("eventLabel", data.label || "NoLabel");
                if (typeof(data.value) != "undefined") {
                    tracker.set("eventValue", data.value);
                }
                console.log("Sending event ", data);
                ga(name  + ".send", "event");
            }
            if (callback) callback();
        });
    };
    var run = function() {
        if (window.ga && window.ga.loaded) {
            sendImpl(event);
        } else {
            if (callback) callback();
        }
    }
    if (typeof(AdminConsole) != "undefined") {
        run();
    } else {
        if (thiz._analyticsScriptLoaded || (window.ga && window.ga.loaded)) {
            run();
        } else {
            widget.__loadScript("/team/" + teamAlias +"/controller/cms/admin/analytics", function(res) {
                thiz._analyticsScriptLoaded = true;
                run();
            });
        }
    }
};

var googleAnalyticsService = {
    CATEGORY: {
        SIGNIN_START: "sso_signin_start",
        FIRSTTIME_SIGNIN: "sso_firsttime_signin",
        STOPSIGN_SIGNIN: "sso_stopsign_signin",
        SIGNIN_ORIGIN: "sso_signin_origin",
        ACCOUNT_PROFILE: "sso_account_profile"
    },
    teamAlias: (typeof(TEAM_INFO) != "undefined" && TEAM_INFO.alias) || (typeof(CONTEXT) != "undefined" && CONTEXT.teamAlias),
    sendAnalyticEvent: sendAnalyticEvent,
    sendSigninClickedEvent: function () {
        this.sendAnalyticEvent({
            category: this.CATEGORY.SIGNIN_START,
            action: "signin_clicked",
            label: this.teamAlias
        });
    },
    sendFirstTimeEvent: function (action, accountId) {
        this.sendAnalyticEvent({
            category: this.CATEGORY.FIRSTTIME_SIGNIN,
            action: action,
            label: this.teamAlias,
            value: accountId
        });
    },
    sendStopSignEvent: function (action, callback) {
        this.sendAnalyticEvent({
            category: this.CATEGORY.STOPSIGN_SIGNIN,
            action: action,
            label: this.teamAlias
        }, callback);
    },
    sendSigninOriginEvent: function (action, callback) {
        this.sendAnalyticEvent({
            category: this.CATEGORY.SIGNIN_ORIGIN,
            action: action,
            label: this.teamAlias
        }, callback);
    },
    sendMainSigninEvent: function(callback) {
        this.sendSigninOriginEvent("main_signin", callback);
    },
    sendMemberRegSigninEvent: function (callback) {
        this.sendSigninOriginEvent("memreg_signin", callback);
    },
    sendClassSigninEvent: function (callback) {
        this.sendSigninOriginEvent("class_signin", callback);
    },
    sendAccountProfileEvent: function (action, accountId) {
        this.sendAnalyticEvent({
            category: this.CATEGORY.ACCOUNT_PROFILE,
            action: action,
            label: this.teamAlias,
            value: accountId
        });
    }
};


if (window) window.__currentScriptPath = "/cms/widgets/Carousel.js";

function Carousel(node) {
    BaseTemplatedWidget.call(this);
    this.index = 0;
    this.duration = Dom.getAttributeAsInt(node, "duration", 650);
    this.scrollMode = Dom.getAttributeAsString(node, "mode", Carousel.MODE_LOOP); // "NONE" | "REVERSED" | "LOOP"
    this.autoSlide = Dom.getAttributeAsInt(node, "auto-slide", 3000);     // set -1 to disable
    this.showStatus = Dom.getAttributeAsBoolean(node, "show-status", true);
    this.notUpscale = Dom.getAttributeAsBoolean(node, "not-upscale", false);
    this.centerCrop = "CENTERCROP" === Dom.getAttributeAsString(node, "scale-type", "CENTERCROP") ? true : false;
    this.bind("click", this.scrollPrev.bind(this, this.duration), this.previousButton);
    this.bind("click", this.scrollNext.bind(this, this.duration), this.nextButton);
    this.bind("touchstart", this._handleTouchStart, this.imageContainer);
    this.bind("touchmove", this._handleTouchMove, this.imageContainer);
    this.bind("touchend", this._handleTouchEnd, this.imageContainer);
    Dom.registerEvent(window, "load", this.onLoad, false);
}
__extend(BaseTemplatedWidget, Carousel);

Carousel.MODE_REVERSED = "reversed";
Carousel.MODE_LOOP = "loop";
Carousel.MODE_NONE = "none";

Carousel.prototype.onAttached = function () {
    if (this._startCheckingLoadedCalled) return;
    this._startCheckingLoadedCalled = true;
    var checker = function () {
        if (this.node().offsetWidth > 0 && this.node().offsetHeight > 0) {
            this._init();
            if (this.scrollMode.toLowerCase() == Carousel.MODE_LOOP) {
                if (this.imageContainer.childNodes.length > 2) this.prepareLooping();
                else this.scrollMode = Carousel.MODE_REVERSED;
            }
        } else {
            window.setTimeout(checker, 100);
        }
    }.bind(this);

    window.setTimeout(checker, 500);
};

Carousel.prototype.onSizeChanged = function () {
    if (!Dom.hasClass(this.node(), "Initialized")) return;

    Dom.doOnSelector(this.imageContainer, "img", function (img) {
        Util.cropImage(img, this.centerCrop, this.notUpscale);
    }.bind(this));
};

Carousel.prototype.setContentFragment = function (fragment) {
    this._providedFragment = fragment;
    console.log("Content fragment set");
};

Carousel.prototype._init = function () {
    var fragment = this._providedFragment;
    var elements = fragment.childNodes;
    if (this.showStatus) {
        this.statusContainer.innerHTML = "";
        Dom.removeClass(this.statusContainer, "Hidden");
    }
    for (var i = 0; i < elements.length; i ++) {
        var element = elements[i];
        if (!element.nodeName || !element.getAttribute) continue;
        element._index = i;
        this.imageContainer.appendChild(element);

        var actionText = Dom.getAttributeAsString(element, "action-text");
        var primaryText = Dom.getAttributeAsString(element, "primary-text");
        var secondaryText = Dom.getAttributeAsString(element, "secondary-text");
        var simpleContentBox = Dom.newDOMElement({_name: "hbox", class: "SimpleContentBox"});
        var contentWrapper = Dom.newDOMElement({_name: "vbox", class: "SimpleContentWrapper"});

        var headingAndContentBox = Dom.newDOMElement({_name: "vbox", class: "OverlayContent"});

        var hasSimpleContentBox = (primaryText && primaryText.length > 0)
        || (secondaryText && secondaryText.length > 0)
        || (actionText && actionText.length > 0);
        var hasPrimaryText = primaryText && primaryText.length > 0;
        var hasSecondaryText = secondaryText && secondaryText.length > 0;
        if (hasPrimaryText) {
            var primaryLabel = Dom.newDOMElement({_name: "span", class: "PrimaryText"});
            Dom.setInnerText(primaryLabel, primaryText);
            headingAndContentBox.appendChild(primaryLabel);
        }
        if (hasSecondaryText) {
            var secondaryLabel = Dom.newDOMElement({_name: "span", class: "SecondaryText"});
            Dom.setInnerText(secondaryLabel, secondaryText);
            headingAndContentBox.appendChild(secondaryLabel);
        }
        if (hasPrimaryText || hasSecondaryText) {
            contentWrapper.appendChild(headingAndContentBox);
        }
        if (actionText && actionText.length > 0) {
            var actionText= Dom.getAttributeAsString(element, "action-text");
            var button = Dom.newDOMElement({_name: "span", class: "Action"});
            var hbox = Dom.newDOMElement({_name: "hbox", class: "ActionWrapper"});
            Dom.setInnerText(button, actionText);
            hbox.appendChild(button);
            contentWrapper.appendChild(hbox);
        }
        if (hasSimpleContentBox) {
            simpleContentBox.appendChild(contentWrapper);
            element.appendChild(simpleContentBox);
        }
        var img = Dom.getTag("img", element);
        var src = img.getAttribute("data-src");
        if (src) {
            src = Util.getBestStorageUrl(src, this.node().offsetWidth, this.node().offsetHeight);
            img.setAttribute("src", src);
            Dom.registerEvent(img, "load", function(event){
                var image = event.target;
                Util.cropImage(image, this.centerCrop, this.notUpscale);
            }.bind(this));
        }
        if (this.showStatus) {
            var icon = Dom.newDOMElement({
                _name: "icon",
                class: "circle" + (i == 0 ? " Active" : ""),
            });
            this.bind("click", this.onIndexClick.bind(this), icon);
            this.statusContainer.appendChild(icon);
        }
    }
    Dom.addClass(this.node(), "Initialized");
    this.startTimer();
};
Carousel.prototype.prepareLooping = function() {
    if (this.scrollMode.toLowerCase() != Carousel.MODE_LOOP) return;
    if (this.index == 0 ) {
        var element = Dom.findLastChildWithClass(this.imageContainer, "SliderItem");
        this.imageContainer.removeChild(element);
        Dom.prepend(element, this.imageContainer);
        this.index++;
        this.scrollTo(this.index, 0);
    }
    if (this.index == this.imageContainer.childNodes.length - 1) {
        var element = Dom.findFirstChildWithClass(this.imageContainer, "SliderItem");
        this.imageContainer.removeChild(element);
        Dom.append(element, this.imageContainer);
        this.index--;
        this.scrollTo(this.index, 0);
    }
}

Carousel.prototype.scrollTo = function(index, duration) {
    var thiz = this;
    var currentActive = Dom.findFirstChildWithClass(this.imageContainer, "Active");
    var element = Dom.getChildByIndex(this.imageContainer, index);
    if (!element) return Promise.resolve();
    var offsetLeft = element.offsetLeft;
    var startLeft = this.imageContainer.scrollLeft;
    var target = Math.round(offsetLeft);
    duration = Math.round(duration);
    Dom.removeClass(currentActive, "Active");
    Dom.addClass(element, "Active");
    if (duration < 0) {
        return Promise.reject("bad duration");
    }
    if (duration === 0) {
        this.imageContainer.scrollLeft = target;
        this.setStatus(parseInt(element._index));
        return Promise.resolve();
    }
    var start_time = Date.now();
    var end_time = start_time + duration;
    var distance = target - startLeft;
    var smooth_step = function(start, end, point) {
        if (point <= start) { return 0; }
        if (point >= end) { return 1; }
        var x = (point - start) / (end - start);
        return x * x * (3 - 2 * x);
    }

    return new Promise(function(resolve, reject) {
        var previous_left = thiz.imageContainer.scrollLeft;
        var scroll_frame = function() {
            if(thiz.imageContainer.scrollLeft != previous_left) {
                reject("interrupted");
                return;
            }
            var now = Date.now();
            var point = smooth_step(start_time, end_time, now);
            var frameLeft = Math.round(startLeft + (distance * point));
            thiz.imageContainer.scrollLeft = frameLeft;
            if ((thiz.imageContainer.scrollLeft === previous_left && thiz.imageContainer.scrollLeft !== frameLeft)
            || (now >= end_time)) {
                thiz.setStatus(parseInt(element._index));
                thiz.startTimer();
                resolve();
                return;
            }
            previous_left = thiz.imageContainer.scrollLeft;
            setTimeout(scroll_frame, 0);
        }
        setTimeout(scroll_frame, 0);
    });
}

Carousel.prototype.scrollPrev = function (duration) {
    var thiz = this;
    if (this.index > 0) this.index--;
    else if (this.scrollMode.toLowerCase() == Carousel.MODE_REVERSED) this.index = this.imageContainer.childNodes.length - 1;
    else return;
    this.scrollTo(this.index, duration).then(function() {
        thiz.prepareLooping();
    });
}

Carousel.prototype.scrollNext = function (duration) {
    var thiz = this;
    if (this.imageContainer.childNodes.length > (this.index + 1)) this.index++;
    else if (this.scrollMode.toLowerCase() == Carousel.MODE_REVERSED) this.index = 0;
    else return;
    this.scrollTo(this.index, duration).then(function() {
        thiz.prepareLooping();
    });
}

Carousel.prototype._handleTouchStart = function(event) {
    this.startX = event.touches[0].clientX;
    this.captureLeft = this.imageContainer.scrollLeft;
    this.clearTimer();
}

Carousel.prototype._handleTouchMove = function(event) {
    // console.log("# HANDLE TouchMove");
    var endX = event.touches[0].clientX;
    var d = endX - this.startX;
    this.imageContainer.scrollLeft = this.captureLeft - d;
}

Carousel.prototype._handleTouchEnd = function(event) {
    var thiz = this;
    var endX = event.changedTouches[0].clientX;
    var d = endX - this.startX;
    var w = this.imageContainer.clientWidth;
    var p = Math.abs(d) * 100 / w;
    var s = (100 - p) / 100;
    if (p > 25) {
        if (d > 0) {
            if (this.index != 0) this.scrollPrev(this.duration * s);
            else this.prepareLooping();
        } else {
            if (this.imageContainer.childNodes.length != (this.index + 1)) this.scrollNext(this.duration * s);
            else this.prepareLooping();
        }
    } else {
        // console.log("re-position current frame");
        this.scrollTo(this.index, this.duration * s).then(function() {
            thiz.prepareLooping();
        });
    }
    this.startX = null;
}

Carousel.prototype.onIndexClick = function(event) {
    var thiz = this;
    var node = event.target;
    var index = Dom.getIndexInParent(node);
    if (Dom.hasClass(node, "Active")) return;
    this.clearTimer();
    if (this.scrollMode.toLowerCase() != Carousel.MODE_LOOP) {
        var currentActive = Dom.findFirstChildWithClass(this.statusContainer, "Active");
        Dom.removeClass(currentActive, "Active");
        Dom.addClass(node, "Active");
        this.scrollTo(index, this.duration).then(function(){
            thiz.index = index;
        });
    } else {
        var activeSlide = Dom.findFirstChildWithClass(this.imageContainer, "Active");
        var slideIndex = Dom.getIndexInParent(activeSlide);
        var currentIndex = parseInt(activeSlide._index);
        var size = this.statusContainer.childNodes.length;
        var step = index - currentIndex;
        var newIndex = slideIndex + step;
        if (newIndex > (size - 1)) newIndex -= size;
        else if (newIndex < 0) newIndex += size;
        this.scrollTo(newIndex, this.duration).then(function() {
            thiz.index = newIndex;
            thiz.prepareLooping();
        }).catch(function(err) {
            console.log("Interrupted:", err);
        });
    }
}

Carousel.prototype.setStatus = function(index) {
    var current = Dom.findChildWithClass(this.statusContainer, "Active");
    Dom.removeClass(current, "Active");
    current = Dom.getChildByIndex(this.statusContainer, index);
    Dom.addClass(current, "Active");
}

Carousel.prototype.clearTimer = function() {
    if (this.timer) {
        clearTimeout(this.timer);
        this.timer = null;
    }
}

Carousel.prototype.startTimer = function() {
    this.clearTimer();
    if (this.autoSlide > 0) this.timer = setTimeout(this.scrollNext.bind(this, this.duration), this.autoSlide);
}


if (window) window.__currentScriptPath = "/cms/widgets/ErrorPage.js";

function ErrorPage(node) {
    BaseWidget.call(this, node);
    this.homeLink = Dom.getAttributeAsString(node, "url-home");
    // if (!document.referrer.startsWith(location.origin)) this.buttonBack.textContent = "Take Me Home";
    this.bind("click", function(event) {
        // if (document.referrer.startsWith(location.origin)) {
        //     location.href = document.referrer;
        // } else {
            location.href = location.origin + this.homeLink;
        // }
    }, this.buttonBack);
}
__extend(BaseWidget, ErrorPage);

ErrorPage.prototype.buildDOMNode = function(definitionNode) {
    return widget.Util.buildDOMFromTemplate(definitionNode, this);
}


if (window) window.__currentScriptPath = "/cms/widgets/MobileCombinedNavigation.js";

function MobileCombinedNavigation() {
    BaseTemplatedWidget.call(this);

    this.bind("click", this.handleMenuButtonClick, this.menuButton);

    MobileCombinedNavigation.COMPONENT_CLASSES.forEach(function (className) {
        var element = document.querySelector("." + className);
        if (!element) return;
        var c = element.cloneNode(true);
        this.menuContainer.appendChild(c);
    }.bind(this));
}
__extend(BaseTemplatedWidget, MobileCombinedNavigation);

MobileCombinedNavigation.COMPONENT_CLASSES = ["CMSComponentSocialLinks", "CMSComponentMainNavigator", "CMSComponentTeamLinks"];

MobileCombinedNavigation.prototype.handleMenuButtonClick = function (event) {
    Dom.toggleClass(this.node(), "Activated");
    if (Dom.hasClass(this.node(), "Activated")) {
        var w = Dom.getOffsetWidth(document.body);
        var minWidth = w >= 920 ? w * 0.5 : w * 0.8;
        Dom.doOnSelector(this.menuContainer, ".CMSComponent", function(node) {
            node.style.minWidth = minWidth + "px";
        });
    }
};


if (window) window.__currentScriptPath = "/cms/widgets/MobileHeader.js";

function MobileHeader() {
    BaseTemplatedWidget.call(this);

    this.bind("click", this.handleMenuButtonClick, this.menuButton);
    MobileHeader.instance = this;
}
__extend(BaseTemplatedWidget, MobileHeader);

MobileHeader.HEADER_COMPONENT_SELECTORS = [".CMSComponentSocialLinks", ".CMSComponentTeamLevel"];
MobileHeader.CONTENT_COMPONENT_SELECTORS = [".CMSComponentMainNavigator ul.MainNavigator"];
MobileHeader.FOOTER_COMPONENT_SELECTORS = [".CMSComponentTeamLinks > ul"];

MobileHeader.REMOVED_COMPONENTS = ["CMSComponentTeamLogo", "CMSComponentMainNavigator", "CMSComponentTeamLevel", "CMSComponentAccountBar", "CMSComponentSocialLinks"];
MobileHeader.EXCLUDED_SECTIONS = ["siteFooter", "mainContent"];

MobileHeader.prototype.onAttached = function (first) {
    if (!first) return;
    this._manipulateDOMTree();
    var thiz = this;
    window.setTimeout(function () {
        thiz._handleOnRendered();
    }, 200);
};
MobileHeader.prototype.registerMenuItem = function(menuItem) {
    var c = menuItem.viewClass || "";
    if (!c) {
        console.log("Invalid menuItem:", menuItem);
        return;
    }
    var v = new c();
    v._registeredOnMobileHeader = true;
    v.into(this.toolItemsContainer);
    Dom.addClass("ToolItem", v.node());
}
MobileHeader.prototype._manipulateDOMTree = function () {
    //Move navigation components into popup sidebar
    MobileHeader.HEADER_COMPONENT_SELECTORS.forEach(function (selector) {
        var element = document.querySelector(selector);
        if (!element) return;
        this.menuHeader.appendChild(element.cloneNode(true));
    }.bind(this));

    MobileHeader.CONTENT_COMPONENT_SELECTORS.forEach(function (selector) {
        var element = document.querySelector(selector);
        if (!element) return;
        this.menuContent.appendChild(element.cloneNode(true));
    }.bind(this));

    MobileHeader.FOOTER_COMPONENT_SELECTORS.forEach(function (selector) {
        var element = document.querySelector(selector);
        if (!element) return;
        this.menuFooter.appendChild(element.cloneNode(true));
    }.bind(this));

    //Parsing logo image
    var img = document.querySelector(".CMSComponentTeamLogo img");
    var showTeamLogo = (SITE_STYLE_MAP && SITE_STYLE_MAP.mobilenav_showTeamLogo == "") ? false : true;
    if (img && showTeamLogo) {
        Dom.addClass(this.node(), "WithLogo");
        var src = CONTEXT.teamLogoMobileUrl || CONTEXT.teamLogoUrl || img.getAttribute("src");

        this.pendingImageSrc = src;
        if (img.parentNode.nodeName.toLowerCase() == "a") {
            var url = img.parentNode.getAttribute("href");
            var target = img.parentNode.getAttribute("target");

            this.logoLink.setAttribute("href", url);
            if (target) this.logoLink.setAttribute("target", target);
        } else {
            this.logoLink.removeAttribute("href");
        }
    }

    //Site name
    this.showTeamName = SITE_STYLE_MAP && ("true" == SITE_STYLE_MAP.mobilenav_showTeamName);
    if (this.showTeamName) Dom.addClass(this.node(), "WithTeamName");
    Dom.setInnerText(this.teamName, CONTEXT.teamName);

    //Moving account bar
    var accountBox = document.querySelector(".CMSComponentAccountBar");
    if (accountBox) {
        this.accountInfoContainer.appendChild(accountBox.cloneNode(true));
    }

    this._invalidateUI();

    Dom.registerEvent(document, "scroll", function (e) {
        var y = document.body.getBoundingClientRect().y;
        var pin = y < 0 - (Math.max(this.node().offsetHeight / 3, 10));
        var pinned = Dom.hasClass(this.node(), "Pinned");

        if (pin != pinned) Dom.toggleClass(this.node(), "Pinned", pin);
    }.bind(this));
};
MobileHeader.prototype._invalidateUI = function () {
    //Handling empty containers
    Dom.doOnSelector(document.body, ".CMSSection", function (section) {
        Dom.removeClass(section, "CMSHasVisibleChild");
    });
    Dom.doOnSelector(document.body, ".CMSSection .CMSSectionSlot .CMSComponent", function (component) {
        var hidden = window.getComputedStyle(component).display == "none" || Dom.hasClass(component, "CMSComponentSpacer");
        if (!hidden) {
            var sectionNode = component.parentNode.parentNode;
            sectionNode._hasVisibleChild = true;
            Dom.addClass(sectionNode, "CMSHasVisibleChild");
        }
    });

    //Handling empty containers
    Dom.doOnSelector(document.body, ".CMSDetectEmpty", function (container) {
        var visibleSection = container.querySelector(".CMSSection.CMSHasVisibleChild");
        Dom.toggleClass(container, "CMSMobileEmptyContainer", visibleSection ? false : true);
    });

    this._invalidateLogoSize();

    var thiz = this;
    window.setTimeout(function () {
        if (thiz.node().offsetHeight > 0) Dialog.TOP_OFFSET = thiz.node().offsetHeight;
    }, 1000);
};
MobileHeader.prototype._invalidateLogoSize = function () {
    if (!this.logoImage._configured) return;
    var height = this.logoLink.offsetHeight;
    var width = this.showTeamName ? height : this.logoLink.offsetWidth;

    if (!this.logoImage.naturalWidth || !this.logoImage.naturalHeight) return;

    var r = Math.max(this.logoImage.naturalWidth / width, this.logoImage.naturalHeight / height);
    var w = Math.round(this.logoImage.naturalWidth / r);
    var h = Math.round(this.logoImage.naturalHeight / r);

    this.logoImage.style.width = w + "px";
    this.logoImage.style.height = h + "px";

    this.logoImage.style.display = "inline-block";
};
MobileHeader.prototype.onSizeChanged = function () {
    this._invalidateUI();
};
MobileHeader.prototype._handleOnRendered = function () {
    if (this.pendingImageSrc) {
        var width = this.node().offsetWidth;
        var height = this.node().offsetHeight;

        var src = Util.getBestStorageUrl(this.pendingImageSrc, width, height);
        this.logoImage.style.display = "none";
        this.logoImage.onload = function () {
            this._invalidateLogoSize();
        }.bind(this);

        this.logoImage._configured = true;
        this.logoImage.setAttribute("src", src);
    }
};
MobileHeader.prototype.handleMenuButtonClick = function (event) {
    Dom.toggleClass(this.node(), "MenuActivated");
};


if (window) window.__currentScriptPath = "/cms/widgets/ReviewShowcase.js";

function ReviewShowcase() {
    BaseTemplatedWidget.call(this);
}
__extend(BaseTemplatedWidget, ReviewShowcase);

ReviewShowcase.prototype.onAttached = function (e) {
    widget.__ensureNamespaceLoaded("bpi", function (bpi) {
        this.bpiModule = bpi;
        this.autoSlide = Dom.getAttributeAsBoolean(this.node(), "auto-slide", false);
        this.slideDelay = Dom.getAttributeAsInt(this.node(), "slide-delay", 10);
        this.loadTeamReviewShowCase();
    }.bind(this), "Loading...");
};
ReviewShowcase.prototype.busy = function (message) {
    if (!this._busyCount) this._busyCount = 0;
    this._busyCount ++;

    Dom.addClass(this.busyIndicator, "Busy");
    Dom.addClass(this.node(), "Busy");
    Dom.setInnerText(this.loadingMessage, message || "Loading...");
};
ReviewShowcase.prototype.done = function () {
    if (typeof this._busyCount == "number" && this._busyCount > 0) this._busyCount --;

    if (this._busyCount == 0) {
        Dom.removeClass(this.busyIndicator, "Busy");
        Dom.removeClass(this.node(), "Busy");
        Dom.setInnerText(this.loadingMessage, "");
    }
};
ReviewShowcase.prototype.failed = function (e) {
};
ReviewShowcase.prototype.progressed = function (loaded, total) {
};
ReviewShowcase.prototype.loadTeamReviewShowCase = function () {
    Dom.empty(this.slideShowContainer);
    $businessPublisherIntegrationService.getCMSComponentReviewShowcaseInfo(true, function (data) {
        if (!data) return;

        var node = Dom.newDOMElement({
            _name: "vbox"
        });
        var reviewShowcaseSlideshow = new this.bpiModule.ReviewShowcaseSlideshow();
        reviewShowcaseSlideshow.initWith(data, {autoSlide: this.autoSlide, slideDelay: this.slideDelay});
        reviewShowcaseSlideshow.into(node);
        this.slideShowContainer.appendChild(node);
    }.bind(this), function (error) {
    }, this);
};


if (window) window.__currentScriptPath = "/cms/widgets/SignInInfoDialog.js";

function SignInInfoDialog(options) {
    BoldDialog.call(this);
    this.options = options;
    this.title = "";
    var src = TEAM_INFO.teamLogoUrl || (typeof(CONTEXT) != "undefined" ? (CONTEXT.teamLogoUrl || CONTEXT.teamLogoMobileUrl) : "");
    src = Util.getBestStorageUrl(src, 300, 300);
    this.bannerImageUrl = src ?  src : "";
    this.titleImage.alt = TEAM_INFO.name || CONTEXT.teamName;
    var thiz = this;
    this.bind("click", function() {
        this.close(false);
        if (thiz.options.contactTeam) {
            if (googleAnalyticsService) {
                googleAnalyticsService.sendStopSignEvent(SignInInfoDialog.STOPSIGNIN_ACTIONS.CONTACT, thiz.options.contactTeam);
            } else {
                thiz.options.contactTeam();
            }
        }
    }, this.contactTeam);

    this.bind("click", function() {
        this.close(false);
        if (thiz.options.contactTeam) {
            if (googleAnalyticsService) {
                googleAnalyticsService.sendStopSignEvent(SignInInfoDialog.STOPSIGNIN_ACTIONS.CONTACT, thiz.options.contactTeam);
            } else {
                thiz.options.contactTeam();
            }
        }
    }, this.contactTeam2);

    this.bind("click", function() {
        this.close(false);
        if (thiz.options.signinOther) {
            if (googleAnalyticsService) {
                googleAnalyticsService.sendStopSignEvent(SignInInfoDialog.STOPSIGNIN_ACTIONS.RESIGNIN, thiz.options.signinOther);
            } else {
                thiz.options.signinOther();
            }
        }
    }, this.signinOther);
    this.registerImageEvent(this.titleImage);

    this.bind("click", function() {
        if (googleAnalyticsService) {
            googleAnalyticsService.sendStopSignEvent(SignInInfoDialog.STOPSIGNIN_ACTIONS.CLOSE, function () {
                thiz.close(true);
            });
        } else {
            thiz.close(true);
        }
    }, this.okButton);
};
__extend(BoldDialog, SignInInfoDialog);

SignInInfoDialog.STOPSIGNIN_ACTIONS = {
    CONTACT: "stopsign_contact",
    RESIGNIN: "stopsign_resignin",
    CLOSE: "stopsign_close",
    DISPLAYED: "stopsign_displayed"
};

SignInInfoDialog.prototype.getDialogActions = function () {
    var thiz = this;
    return [{
        run: function() {
            if (googleAnalyticsService) {
                googleAnalyticsService.sendStopSignEvent(SignInInfoDialog.STOPSIGNIN_ACTIONS.CLOSE, function () {
                    thiz.close(true);
                });
            } else {
                thiz.close(true);
            }
            return false;
        },
        title : "Close",
        type: "cancel"
    }];
};
SignInInfoDialog.prototype.onHide = function() {
    if (this.options.onHide) this.options.onHide();
}
SignInInfoDialog.prototype.asyncSetup = function (data, callback) {
    var msg = this.options.message || "";
    msg = msg.replace(/{TEAM_NAME}/g, TEAM_INFO.name || CONTEXT.teamName);
    this.descriptionText.innerHTML = msg;
    this.okButton.innerHTML = this.options.actionButtonText || "OK";
    msg = "Click here to contact " + (TEAM_INFO.name || CONTEXT.teamName);
    this.contactTeam2.innerHTML = msg;
    if (googleAnalyticsService) {
        googleAnalyticsService.sendStopSignEvent(SignInInfoDialog.STOPSIGNIN_ACTIONS.DISPLAYED);
    }
    Dom.toggleClass(this.actionsContainer, "Active", this.options.actionsEnabled ? true : false);
    Dom.toggleClass(this.contactUsBox, "Active", !this.options.actionsEnabled ? true : false);
    if (callback) callback();
};

SignInInfoDialog.prototype.registerImageEvent = function (image) {
    this.bind("load", function () {
        Dom.removeClass(image, "NoImage");
    }, image);
    this.bind("error", function () {
        Dom.addClass(image, "NoImage");
    }, image);
};


if (window) window.__currentScriptPath = "/cms/widgets/SignInNotificationDialog.js";

function SignInNotificationDialog(signinCallback) {
    BoldDialog.call(this);
    this.signinCallback = signinCallback;
    var teamLogo = (typeof (CONTEXT) != "undefined" && (CONTEXT.teamLogoUrl || CONTEXT.teamLogoMobileUrl))
        || (typeof (TEAM_INFO) != "undefined" && TEAM_INFO.teamLogoUrl) || "";
    teamLogo = Util.getBestStorageUrl(teamLogo, 300, 300);
    this.title = "";
    this.bannerImageUrl = teamLogo ?  teamLogo : "";
    this.titleImage.alt = TEAM_INFO.name || CONTEXT.teamName;
    this.bind("click", this.signin, this.signinButton);
    this.registerImageEvent(this.titleImage);

    this.bind("click", function() {
        (window.wrappedWindowLocation || window.location).href = "/ContactMe.do?act=new&team=" + this.getTeamAlias();
    }, this.contactTeam);

    if (SERVER_FLAVOR == "SO") {
        this.teamRegistrationButton.innerHTML = "Continue to Team Registration";
    }
}
__extend(BoldDialog, SignInNotificationDialog);

SignInNotificationDialog.prototype.getDialogActions = function() {
    return [
        {
            type: "accept", title: "Sign in",
            run: function() {
                this.signin();
                return true;
            }
        },
        Dialog.ACTION_CANCEL
    ];
};

SignInNotificationDialog.prototype.signin = function() {
    if (this.signinCallback) this.signinCallback();
    this.close();
};

SignInNotificationDialog.prototype.getTeamAlias = function () {
    return (typeof(TEAM_INFO) != "undefined" && TEAM_INFO.alias) || (typeof(CONTEXT) != "undefined" && CONTEXT.teamAlias);
}

SignInNotificationDialog.prototype.setup = function(options) {
    var teamName = (typeof (CONTEXT) != "undefined" && CONTEXT.teamName) || (typeof (TEAM_INFO) != "undefined" && TEAM_INFO.name) || "";
    this.title = teamName;
    this.replaceTeamTexts(this.titleText, this.actionsTitle, this.contactTeam);

    var showClass = options.isShowClassRegistrationLink;
    Dom.toggleClass(this.actionsBox, "ShowClass", showClass);
    var classText = options.classRegistrationLinkLabel;
    if (showClass && classText) {
        this.classRegistrationButton.innerHTML = classText;
    }
    if (options.isClassRegistrationCustomLinkEnabled && options.classRegistrationLink) {
        var classRegistrationLink = options.classRegistrationLink.trim();
        if (!classRegistrationLink.match(ValidationManager.patterns.RELATIVE_URL_REGEX)) {
            classRegistrationLink = "https://" + classRegistrationLink;
        }
        this.classRegistrationButton.href = classRegistrationLink;
    } else {
        this.classRegistrationButton.href = options.publicClassRegistrationLink || "";
    }

    var showTeam = options.isShowTeamRegistrationLink;
    Dom.toggleClass(this.actionsBox, "ShowTeam", showTeam);
    var teamText = options.teamRegistrationLinkLabel;
    if (showTeam && teamText) {
        this.teamRegistrationButton.innerHTML = teamText;
    }
    if (options.isTeamRegistrationCustomLinkEnabled && options.teamRegistrationLink) {
        var teamRegistrationLink = options.teamRegistrationLink.trim();
        if (!teamRegistrationLink.match(ValidationManager.patterns.RELATIVE_URL_REGEX)) {
            teamRegistrationLink = "https://" + teamRegistrationLink;
        }
        this.teamRegistrationButton.href = teamRegistrationLink;
    } else {
        this.teamRegistrationButton.href = options.publicTeamRegistrationLink || "";
    }
    
    var showBooking = options.isShowBookingRegistrationLink;
    Dom.toggleClass(this.actionsBox, "ShowBooking", showBooking);
    var bookingText = options.bookingRegistrationLinkLabel;
    if (showBooking && bookingText) {
        this.bookingRegistrationButton.innerHTML = bookingText;
    }
    if (options.isBookingRegistrationCustomLinkEnabled && options.bookingRegistrationLink) {
        var bookingRegistrationLink = options.bookingRegistrationLink.trim();
        if (!bookingRegistrationLink.match(ValidationManager.patterns.RELATIVE_URL_REGEX)) {
            bookingRegistrationLink = "https://" + bookingRegistrationLink;
        }
        this.bookingRegistrationButton.href = bookingRegistrationLink;
    } else {
        this.bookingRegistrationButton.href = options.publicBookingRegistrationLink || "";
    }
};

SignInNotificationDialog.prototype.replaceTeamTexts = function () {
    var teamName = (typeof (CONTEXT) != "undefined" && CONTEXT.teamName) || (typeof (TEAM_INFO) != "undefined" && TEAM_INFO.name) || "";
    for (var i = 0; i < arguments.length; i ++) {
        var node = arguments[i];
        var msg = node.innerHTML;
        msg = msg.replace(/{TEAM_NAME}/g, teamName);
        node.innerHTML = msg;
    };
};

SignInNotificationDialog.prototype.registerImageEvent = function (image) {
    this.bind("load", function () {
        Dom.removeClass(image, "NoImage");
    }, image);
    this.bind("error", function () {
        Dom.addClass(image, "NoImage");
    }, image);
};


if (window) window.__currentScriptPath = "/cms/widgets/UpcomingEvents.js";

function UpcomingEvents() {

    BaseTemplatedWidget.call(this);
    var thiz = this;
    this.eventList.populator = this.pupulateEventList.bind(this);

    this.bind("click", function(event){
        var node = Dom.findUpwardForNodeWithData(event.target, "_data");
        if (!node) return;
        thiz.activeCategory(node);
    }, this.selectorPanes);

    this.bind("click", function(event){
        var target = Dom.getTarget(event);
        if (!target) return;

        var eventItemNode = Dom.findUpwardForNodeWithData(target, "_data");
        if (!eventItemNode) return;

        if (Dom.findParentWithClass(target, "Button") || Dom.findParentWithClass(target, "Title")) {
            return;
        }

        Dom.cancelEvent(event);
        if (eventItemNode._url) {
            window.open(eventItemNode._url, "_self");
        }
    }, this.eventList);

}
__extend(BaseTemplatedWidget, UpcomingEvents);

UpcomingEvents.prototype.zPadValue = function(num) {
    if (num < 10) return "0" + num;
    return num;
}
UpcomingEvents.prototype.busy = function (message) {
    if (!this._busyCount) this._busyCount = 0;
    this._busyCount ++;

    Dom.addClass(this.busyIndicator, "Busy");
    Dom.addClass(this.node(), "Busy");
    Dom.setInnerText(this.loadingMessage, message || "Please wait...");
};
UpcomingEvents.prototype.done = function () {
    if (typeof this._busyCount == "number" && this._busyCount > 0) this._busyCount --;

    if (this._busyCount == 0) {
        Dom.removeClass(this.busyIndicator, "Busy");
        Dom.removeClass(this.node(), "Busy");
        Dom.setInnerText(this.loadingMessage, "");
    }
};
UpcomingEvents.prototype.failed = function (e) {
};
UpcomingEvents.prototype.progressed = function (loaded, total) {
};

UpcomingEvents.MORE_EVENT_BY_CATEGORY = {};
UpcomingEvents.MORE_EVENT_BY_CATEGORY["general"] = "#/views/list";
UpcomingEvents.MORE_EVENT_BY_CATEGORY["teamevent"] = "#/team-events/upcoming";
UpcomingEvents.MORE_EVENT_BY_CATEGORY["practice"] = "#/practices/list";
UpcomingEvents.BUTTON_TEXT_BY_CATEGORY = {
    general: "View Calendar",
    teamevent:"More Events",
    practice: "View Calendar",
};

UpcomingEvents.prototype.loadEventFor = function(category) {
    var compId = Dom.getAttributeAsInt(this.node(), "comp-id", 0);
    var thiz = this;
    $eventService.getUpcommingEventByCategory(category.key, compId, function(r) {
        thiz.eventList.setItems(r.result);
        var urlByCatergory = CALENDAR_PATH + (UpcomingEvents.MORE_EVENT_BY_CATEGORY[category.key] || "");

        if (typeof(UpcomingEvents.MORE_EVENT_BY_CATEGORY[category.key]) == "undefined") {
            urlByCatergory = "";
        }
        if (urlByCatergory && urlByCatergory.length > 0) {
            thiz.moreCalendarLink.innerHTML = UpcomingEvents.BUTTON_TEXT_BY_CATEGORY[category.key];
            thiz.moreCalendarLink.href = urlByCatergory;
            Dom.addClass(thiz.moreCalendarLink, "Active");
        } else {
            Dom.removeClass(thiz.moreCalendarLink, "Active");
            thiz.moreCalendarLink.href = "";
        }
        //BaseWidget.invalidateResponsiveBreakpointClasses(thiz.node());

    }, function(e){
        thiz.eventList.setItems([]);
    }, this);
}


UpcomingEvents.prototype.setContentFragment = function (fragment) {
    Dom.empty(this.selectorPanes);
    var thiz = this;
    var compId = Dom.getAttributeAsInt(this.node(), "comp-id", 0);
    $eventService.getEventCategories(compId, function(r) {
        var categories = r || [];
        if (!categories || categories.length == 0) return;
        for (var i = 0; i < categories.length; i++) {
            var category = categories[i];
            var customizedHeader = Dom.getAttributeAsString(thiz.node(), category.key || "", "");
            var node = Dom.newDOMElement({
               _name: "div",
               "class": "TypeSelector " + category.key,
               categoryId: category.key,
               _text: customizedHeader || category.name});
            node._data = category;
            node.style.color = category.color || "#000";
            thiz.selectorPanes.appendChild(node);
        }
        if (categories.length > 0) {
            thiz.activeCategory(thiz.selectorPanes.firstChild);
        }
    }, function(err){
        console.log("Get Event Category fail with eror:", err);
    });
}

UpcomingEvents.prototype.pupulateEventList = function (data, binding, node, index) {
    var eventItem = null;
    switch(data.event_type_key.value) {
        case "reminders":
            eventItem = new ReminderEventItem();
            break;
        case "practice":
            eventItem = new PracticeEventItem();
            break;
        case "general":
            eventItem = new GeneralEventItem();
            break;
        case "registration":
            eventItem = new RegistrationEventItem();
            break;
        case "teamevent":
            eventItem = new TeamEventItem();
            break;
        case "fundraising":
            eventItem = new FundraisingEventItem();
            break;
        default:
            break;
    }
    if (eventItem && eventItem.pupulator) {
        eventItem.pupulator(data, binding, node, index);
    }
}

UpcomingEvents.prototype.activeCategory = function(node) {
    if (Dom.hasClass(node, "Active")) return;
    var currentActive = Dom.findChildWithClass(node.parentNode, "Active");
    if (currentActive && currentActive._data) {
        Dom.removeClass(currentActive, "Active");
        currentActive.style.borderBottomColor = "";
        currentActive.style.color = currentActive._data.color;
        currentActive.style.backgroundColor = currentActive._data.color && currentActive._data.color.toLowerCase() == "#ffffff" ? "#c3c3c3" : "#FFFFFF";
        currentActive.style.borderLeftColor = currentActive._data.borderColor;
    }
    if (node._data && node._data) {
        var config = node._data;
        Dom.addClass(node, "Active");
        node.style.borderLeftColor = config.color;
        node.style.backgroundColor = config.backgroundColor;
        node.style.borderBottomColor = config.color;
        node.style.color = config.color;
        this.categoryHeader.style.color = config.color;
        this.categoryHeader.innerHTML = config.name;
        this._showCategory = config;
        this.contentPane.style.borderLeftColor = config.borderColor;
    }
    this.loadEventFor(node._data);
}

var BaseEventItem = function() {
    this.months = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"];
    this.actionButtons = {
        edit: ["Edit", "Primary", "pencil"],
        cancel: ["Cancel", "Danger", "close-circle"],
        delete: ["Delete", "Danger", "delete"],
        attend: ["Attend/Decline", "Primary", "pencil"],
        register: ["Register Online", "Primary", "account-circle"],
        signup: ["Job Signup", "Primary", "tune"],
        editSignup: ["Edit Job Signup", "Primary", "tune"],
        editCommitment: ["Edit Commitment", "Primary", "pencil"],
        donate: ["Make a Donation", "Primary", "cash"]
    };
    this._data = {};
}

BaseEventItem.prototype = {
    pupulator: function(data, binding, node, index) {
        this.binding = binding;
        this._data = data;
        this.binding._node._data = data;
        var isAllDay = this._data.isAllDay.value;
        Dom.toggleClass(this.binding._node, "AllDay", isAllDay);
        var startDate = data.event_start.value;
        var endDate = data.event_end.value;
        var startDateMoment = undefined;
        if (typeof(startDate) != "undefined" && typeof(startDate.getDate) == "function") {
            try {
                if (isAllDay && CONTEXT.teamTimeZone) {
                    startDateMoment = moment(startDate).tz(CONTEXT.teamTimeZone);
                } else {
                    startDateMoment = moment(startDate);
                }
            } catch(e) {
            };
        }
        if (startDateMoment) {
            Dom.setInnerText(binding.startDate, startDateMoment.format(CMSCommon.getMomentFormat("DD")));
            Dom.setInnerText(binding.year, startDateMoment.format(CMSCommon.getMomentFormat("YYYY")));
            Dom.setInnerText(binding.month, startDateMoment.format(CMSCommon.getMomentFormat("MMM")));
        } else {
            Dom.setInnerText(binding.startDate, "");
            Dom.setInnerText(binding.year, "");
            Dom.setInnerText(binding.month, "");
        }
        var hasEndDate = typeof(endDate) != "undefined" && typeof(endDate.getDate) == "function";
        Dom.toggleClass(binding.endDate, "NoEndDate", !hasEndDate);
        if (hasEndDate) {
            var endDateMoment = undefined;
            if (isAllDay && CONTEXT.teamTimeZone) {
                endDateMoment = moment(endDate).tz(CONTEXT.teamTimeZone);
            } else {
                endDateMoment = moment(endDate);
            }
            Dom.setInnerText(binding.endDate, endDateMoment.format(CMSCommon.getMomentFormat("DD")));
        } else {
            Dom.setInnerText(binding.endDate, "");
        }

        var url = this.getEventDetailUrl ? this.getEventDetailUrl() : "";
        if (url && url.length > 0) {
            binding.title.href = url;
            Dom.setInnerText(binding.title, data.title.displayValue || "");
            binding.titleLabel.style.display = "none";
            binding.title.style.display = "flex";
            node._url = url;
        } else {
            Dom.setInnerText(binding.titleLabel, data.title.displayValue || "");
            binding.titleLabel.style.display = "flex";
            binding.title.style.display = "none";
        }
        this.generateInfoData();
        this.pupulateActionContainer();

        Dom.removeClass(this.binding._node, "Odd");
        Dom.removeClass(this.binding._node, "Even");
        Dom.addClass(this.binding._node, (index % 2 == 0) ? "Even" : "Odd");
    },
    zPadValue: function(num) {
        if (num < 10) return "0" + num;
        return num;
    },
    // getInfoData: function() {},
    generateInfoData: function() {
        var endDate = this._data.event_end.value;
        var startDate = this._data.event_start.value;
        var locations = this._data.location_name.value;
        var rosters = this._data.roster_group_name.value;
        var isAllDay = this._data.isAllDay.value;
        var location = "";
        var roster = "";
        if (locations) {
            location = locations.join(", ");
        }
        if (rosters) {
            roster = rosters.join(", ");
        }
        var momentS = undefined;
        var momentE = undefined;
        var dateOnlyFormat = CMSCommon.getMomentFormat("MMM DD YYYY");
        var dateTimeFormat = CMSCommon.getMomentFormat("MMM DD YYYY h:mm a");
        var timeOnlyFormat = CMSCommon.getMomentFormat("h:mm a");
        if (typeof(startDate) != "undefined" && typeof(startDate.getDate) == "function") {
            try {
                momentS = moment(startDate);
            } catch(e) {
            };
        }
        var end = "";
        if (typeof(endDate) != "undefined" && typeof(endDate.getDate) == "function") {
            try {
                momentE = moment(endDate);
            } catch(e) {};
        }
        var sameDay = false;
        if (typeof(momentS) != "undefined" && typeof(momentE) != "undefined") {
            sameDay = momentS.isSame(momentE, "day") && momentS.isSame(momentE, "month") && momentS.isSame(momentE, "year");
        }
        Dom.empty(this.binding.infoDataContainer);

        var dateBox = Dom.newDOMElement({_name: "hbox", class: "DateBox"});
        Dom.toggleClass(dateBox, "AllDay", this._data.isAllDay.value);
        Dom.toggleClass(dateBox, "SameDay", sameDay);

        if (momentS) {
            var startValue = "";
            if (isAllDay && CONTEXT.teamTimeZone) {
                startValue = moment(startDate).tz(CONTEXT.teamTimeZone).format(dateOnlyFormat);
            } else {
                startValue = momentS.format(dateOnlyFormat);
            }
            dateBox.appendChild(createInfoView({
                key: "StartDate",
                value: startValue
            }));
        }
        if (momentE) {
            var endValue = "";
            if (isAllDay && CONTEXT.teamTimeZone) {
                endValue = moment(endDate).tz(CONTEXT.teamTimeZone).format(dateOnlyFormat);
            } else {
                endValue = momentE.format(dateOnlyFormat);
            }
            dateBox.appendChild(createInfoView({
                key: "EndDate",
                value: endValue
            }));
        }
        this.binding.infoDataContainer.appendChild(dateBox);

        var isRegistrationEvent = this._data.event_type_key && this._data.event_type_key.value == "registration";

        if (!isAllDay && !isRegistrationEvent) {
            var timeBox = Dom.newDOMElement({_name: "hbox", class: "TimeBox"});
            Dom.toggleClass(timeBox, "AllDay", this._data.isAllDay.value);
            Dom.toggleClass(timeBox, "SameDay", sameDay);
            if (momentS) {
                timeBox.appendChild(createInfoView({
                    key: "StartTime",
                    value: momentS.format(timeOnlyFormat)
                }));
            }
            if (momentE) {
                timeBox.appendChild(createInfoView({
                    key: "EndTime",
                    value: momentE.format(timeOnlyFormat)
                }));
            }
            this.binding.infoDataContainer.appendChild(timeBox);
        }

        var rosterLocationBox = Dom.newDOMElement({_name: "hbox", class: "RosterLocationBox"});
        if (roster.length > 0) {
            rosterLocationBox.appendChild(createInfoView({
                key: "Rosters",
                value: roster
            }));
        }
        if (location.length > 0) {
            if (roster.length > 0) {
                rosterLocationBox.appendChild(createInfoView({
                    key: "Separator",
                    value: " / "
                }));
            }
            rosterLocationBox.appendChild(createInfoView({
                key: "Locations",
                value: location
            }));
        }

        this.binding.infoDataContainer.appendChild(rosterLocationBox);

        var infoData = this.getInfoData ? this.getInfoData() : [];
        if (infoData && infoData.length > 0) {
            for (i in infoData) {
                var v = createInfoView(infoData[i]);
                if (v != null) Dom.append(v, this.binding.infoDataContainer);
            }
        }
        function createInfoView(data) {
            if (!data.value.trim()) return null;
            var c = [];
            var v = { _name: "span", class: "DisplayValue " + data.key, _html: data.value };
            c.push(v);
            return Dom.newDOMElement({
                _name: "hbox",
                _children: c
            });
        }
    },
    // getActionButtons() {},
    pupulateActionContainer: function() {
        var actionButtons = this.getActionButtons ? this.getActionButtons() : [];
        Dom.removeClass(this.binding.actionContainer, "HasActions");
        if (actionButtons.length > 0) {
            Dom.addClass(this.binding.actionContainer, "HasActions");
        }
        if (actionButtons && actionButtons.length > 0) {
            for (button in actionButtons) {
                Dom.append(createActionButton.bind(this)(actionButtons[button]), this.binding.actionContainer);
            }
        }
        function createActionButton (button) {
            var url = button.action();
            var b = Dom.newDOMElement({
                _name: "a",
                class: "Button " + button.data[1],
                "href": url,
                "target": button.target || "_self",
                _children: [
                    {
                        _name: "icon",
                        class: button.data[2]
                    }, {
                        _name: "span",
                        _text: button.data[0]
                    }
                ]
            });
            return b;
        }
    },
    deleteEvent: function(eventObj) {
        Dialog.confirm("Are you sure you want to delete this event?", "",
            "Delete", function() {

            },
            "Cancel", function() {});
    },
    bind: function (eventName, f, node) {
        var n = node || this.__node;
        var thiz = this;
        n.addEventListener(eventName, function (event) {
            f.apply(thiz, [event]);
        });
    }
}

var GeneralEventItem = function() {
    BaseEventItem.call(this);

    this.getEventDetailUrl = function() {
        var id = this._data.event_id.value;
        var url = CALENDAR_PATH + "#/views/week?id=" + id;
        return url;
    };
    this.getInfoData = function() {
        return [
        ]
    };
    this.getActionButtons = function() {
        return [
        ];
    };
}
__extend(BaseEventItem, GeneralEventItem);

var FundraisingEventItem = function() {
    BaseEventItem.call(this);
    this.getInfoData = function() {
        return [
        ]
    };
    this.getActionButtons = function() {
        return [
            {
                data: this.actionButtons.donate,
                action: function() {
                    return (TU_MONEY_PATH || "");
                }
            }
        ];
    };
}
__extend(BaseEventItem, FundraisingEventItem);

var PracticeEventItem = function() {
    BaseEventItem.call(this);
    this.getEventDetailUrl = function() {
        var id = this._data.event_id.value;
        var url = CALENDAR_PATH + "#/practices/week?id=" + id + "&range=" + MAX_WITH_IN_UPCOMING_DAY;
        return url;
    };
    this.getInfoData = function() {
        return [
            // {key: "", value: ""}
        ]
    };
    this.getActionButtons = function() {
        return [];
    };
}
__extend(BaseEventItem, PracticeEventItem);

var RegistrationEventItem = function() {
    BaseEventItem.call(this);
    this.getInfoData = function() {
        return []
    };
    this.getEventDetailUrl = function() {
        var id = this._data.event_id.value;
        if (CONTEXT.accountTeamId == CONTEXT.teamId) {
            return (IS_USING_DOMAIN ? "" : "/team/" + CONTEXT.teamAlias) + "/controller/cms/admin/index#/team-registration/reg:" + id;
        } else {
            var url = (TEAM_REGISTRATION_PATH || "");
            if (url.indexOf("?url=") >= 0) {
                url += encodeURIComponent("&reg_id=" + id);
            } else {
                url += "?reg_id=" + id;
            }
            return url;
        }
    };
    this.getActionButtons = function() {
        var thiz = this;
        return [
            {
                data: this.actionButtons.register,
                action: function() {
                    return thiz.getEventDetailUrl();
                },
                target: "_blank"
            }
        ]
    };
}
__extend(BaseEventItem, RegistrationEventItem);

var ReminderEventItem = function() {
    BaseEventItem.call(this);
    this.getInfoData = function() {
        return [
        ]
    };
    this.getActionButtons = function() {
        return [
        ];
    };
}
__extend(BaseEventItem, ReminderEventItem);

var TeamEventItem = function() {
    BaseEventItem.call(this);
    this.getInfoData = function() {
        return [
        ]
    };
    this.getEventDetailUrl = function() {
        var id = this._data.event_id.value;
        var url = CALENDAR_PATH + "#/team-events/upcoming/" + id;
        return url;
        /*var returnPage = "/team/" + CONTEXT.teamAlias + "/page/home";
        var url = "/EventShow.jsp?returnPage=" + encodeURIComponent(returnPage) + "&id=" + id + "&team=" + CONTEXT.teamAlias;
        return "../page/legacy?url=" + encodeURIComponent(url);*/
    };

    this.getActionButtons = function() {
        var data = this._data;
        var buttons = [
        ];
        if (data.eventInfoField.swimMeetInfo.commitment != "HIDDEN") {

            if (data.eventInfoField.swimMeetInfo.commitment == "EDIT_COMMITMENT") {
                buttons.push({
                    data: this.actionButtons.editCommitment,
                    action: function() {
                        var url = (IS_USING_DOMAIN ? "" : "/team/" + CONTEXT.teamAlias) + "/controller/cms/admin/index#/calendar-team-events/ev:" + data.event_id.value;
                        return url;
                    },
                    target: "_blank"
                });
            }

            if (data.eventInfoField.swimMeetInfo.commitment == "REGISTER_ONLINE") {
                buttons.push({
                    data: this.actionButtons.register,
                    action: function() {
                        var id = data.event_id.value;
                        if (CONTEXT.accountTeamId == CONTEXT.teamId) {
                            return (IS_USING_DOMAIN ? "" : "/team/" + CONTEXT.teamAlias) + "/controller/cms/admin/index#/team-registration/ev:" + id;
                        } else {
                            var url = (TEAM_REGISTRATION_PATH || "");
                            if (url.indexOf("?") >= 0) {
                                url += encodeURIComponent("&event_id=" + id);
                            } else {
                                url += "?event_id=" + id;
                            }
                            return url;
                        }
                    },
                    target: "_blank"
                });
            }

            if (data.eventInfoField.swimMeetInfo.commitment == "ATTEND_DECLINE") {
                buttons.push({
                    data: this.actionButtons.attend,
                    action: function() {
                        return (IS_USING_DOMAIN ? "" : "/team/" + CONTEXT.teamAlias) + "/controller/cms/admin/index#/calendar-team-events/ev:" + data.event_id.value;
                    },
                    target: "_blank"
                });
            }
        }
        if (data.eventInfoField.swimMeetInfo.jobSignup != "HIDDEN") {
            buttons.push({
                data: data.eventInfoField.swimMeetInfo.jobSignup == "EDIT_JOB_SIGNUP" ? this.actionButtons.editSignup : this.actionButtons.signup,
                action: function() {
                    return (IS_USING_DOMAIN ? "" : "/team/" + CONTEXT.teamAlias) + "/controller/cms/admin/index#/calendar-team-events/job:" + data.event_id.value;
                },
                target: "_blank"
            });
        }
        return buttons;
    };
}
__extend(BaseEventItem, TeamEventItem);

if (window) window.__currentScriptPath = null;
