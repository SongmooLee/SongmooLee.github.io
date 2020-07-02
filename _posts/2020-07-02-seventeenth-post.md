---
title: "스터디 13주차"
date: 2020-07-02
categories: Study
---

### temp

#### 1번째

frameworks/base/core/java/android/view/WindowManager.java 의 2895 줄

[링크](https://cs.android.com/android/platform/superproject/+/master:frameworks/base/core/java/android/view/WindowManager.java;l=78?q=windowmanager&sq=)

```
public final int copyFrom(LayoutParams o) {
            int changes = 0;

            if (width != o.width) {
                width = o.width;
                changes |= LAYOUT_CHANGED;
            }
            if (height != o.height) {
                height = o.height;
                changes |= LAYOUT_CHANGED;
            }
            if (x != o.x) {
                x = o.x;
                changes |= LAYOUT_CHANGED;
            }
            if (y != o.y) {
                y = o.y;
                changes |= LAYOUT_CHANGED;
            }
            if (horizontalWeight != o.horizontalWeight) {
                horizontalWeight = o.horizontalWeight;
                changes |= LAYOUT_CHANGED;
            }
            if (verticalWeight != o.verticalWeight) {
                verticalWeight = o.verticalWeight;
                changes |= LAYOUT_CHANGED;
            }
            if (horizontalMargin != o.horizontalMargin) {
                horizontalMargin = o.horizontalMargin;
                changes |= LAYOUT_CHANGED;
            }
            if (verticalMargin != o.verticalMargin) {
                verticalMargin = o.verticalMargin;
                changes |= LAYOUT_CHANGED;
            }
            if (type != o.type) {
                type = o.type;
                changes |= TYPE_CHANGED;
            }
            if (flags != o.flags) {
                final int diff = flags ^ o.flags;
                if ((diff & (FLAG_TRANSLUCENT_STATUS | FLAG_TRANSLUCENT_NAVIGATION)) != 0) {
                    changes |= TRANSLUCENT_FLAGS_CHANGED;
                }
                flags = o.flags;
                changes |= FLAGS_CHANGED;
            }
            if (privateFlags != o.privateFlags) {
                privateFlags = o.privateFlags;
                changes |= PRIVATE_FLAGS_CHANGED;
            }
            if (softInputMode != o.softInputMode) {
                softInputMode = o.softInputMode;
                changes |= SOFT_INPUT_MODE_CHANGED;
            }
            if (layoutInDisplayCutoutMode != o.layoutInDisplayCutoutMode) {
                layoutInDisplayCutoutMode = o.layoutInDisplayCutoutMode;
                changes |= LAYOUT_CHANGED;
            }
            if (gravity != o.gravity) {
                gravity = o.gravity;
                changes |= LAYOUT_CHANGED;
            }
            if (format != o.format) {
                format = o.format;
                changes |= FORMAT_CHANGED;
            }
            if (windowAnimations != o.windowAnimations) {
                windowAnimations = o.windowAnimations;
                changes |= ANIMATION_CHANGED;
            }
            if (token == null) {
                // NOTE: token only copied if the recipient doesn't
                // already have one.
                token = o.token;
            }
            if (packageName == null) {
                // NOTE: packageName only copied if the recipient doesn't
                // already have one.
                packageName = o.packageName;
            }
            if (!Objects.equals(mTitle, o.mTitle) && o.mTitle != null) {
                // NOTE: mTitle only copied if the originator set one.
                mTitle = o.mTitle;
                changes |= TITLE_CHANGED;
            }
            if (alpha != o.alpha) {
                alpha = o.alpha;
                changes |= ALPHA_CHANGED;
            }
            if (dimAmount != o.dimAmount) {
                dimAmount = o.dimAmount;
                changes |= DIM_AMOUNT_CHANGED;
            }
            if (screenBrightness != o.screenBrightness) {
                screenBrightness = o.screenBrightness;
                changes |= SCREEN_BRIGHTNESS_CHANGED;
            }
            if (buttonBrightness != o.buttonBrightness) {
                buttonBrightness = o.buttonBrightness;
                changes |= BUTTON_BRIGHTNESS_CHANGED;
            }
            if (rotationAnimation != o.rotationAnimation) {
                rotationAnimation = o.rotationAnimation;
                changes |= ROTATION_ANIMATION_CHANGED;
            }

            if (screenOrientation != o.screenOrientation) {
                screenOrientation = o.screenOrientation;
                changes |= SCREEN_ORIENTATION_CHANGED;
            }

            if (preferredRefreshRate != o.preferredRefreshRate) {
                preferredRefreshRate = o.preferredRefreshRate;
                changes |= PREFERRED_REFRESH_RATE_CHANGED;
            }

            if (preferredDisplayModeId != o.preferredDisplayModeId) {
                preferredDisplayModeId = o.preferredDisplayModeId;
                changes |= PREFERRED_DISPLAY_MODE_ID;
            }

            if (systemUiVisibility != o.systemUiVisibility
                    || subtreeSystemUiVisibility != o.subtreeSystemUiVisibility) {
                systemUiVisibility = o.systemUiVisibility;
                subtreeSystemUiVisibility = o.subtreeSystemUiVisibility;
                changes |= SYSTEM_UI_VISIBILITY_CHANGED;
            }

            if (hasSystemUiListeners != o.hasSystemUiListeners) {
                hasSystemUiListeners = o.hasSystemUiListeners;
                changes |= SYSTEM_UI_LISTENER_CHANGED;
            }

            if (inputFeatures != o.inputFeatures) {
                inputFeatures = o.inputFeatures;
                changes |= INPUT_FEATURES_CHANGED;
            }

            if (userActivityTimeout != o.userActivityTimeout) {
                userActivityTimeout = o.userActivityTimeout;
                changes |= USER_ACTIVITY_TIMEOUT_CHANGED;
            }

            if (!surfaceInsets.equals(o.surfaceInsets)) {
                surfaceInsets.set(o.surfaceInsets);
                changes |= SURFACE_INSETS_CHANGED;
            }

            if (hasManualSurfaceInsets != o.hasManualSurfaceInsets) {
                hasManualSurfaceInsets = o.hasManualSurfaceInsets;
                changes |= SURFACE_INSETS_CHANGED;
            }

            if (preservePreviousSurfaceInsets != o.preservePreviousSurfaceInsets) {
                preservePreviousSurfaceInsets = o.preservePreviousSurfaceInsets;
                changes |= SURFACE_INSETS_CHANGED;
            }

            if (needsMenuKey != o.needsMenuKey) {
                needsMenuKey = o.needsMenuKey;
                changes |= NEEDS_MENU_KEY_CHANGED;
            }

            if (accessibilityIdOfAnchor != o.accessibilityIdOfAnchor) {
                accessibilityIdOfAnchor = o.accessibilityIdOfAnchor;
                changes |= ACCESSIBILITY_ANCHOR_CHANGED;
            }

            if (!Objects.equals(accessibilityTitle, o.accessibilityTitle)
                    && o.accessibilityTitle != null) {
                // NOTE: accessibilityTitle only copied if the originator set one.
                accessibilityTitle = o.accessibilityTitle;
                changes |= ACCESSIBILITY_TITLE_CHANGED;
            }

            if (mColorMode != o.mColorMode) {
                mColorMode = o.mColorMode;
                changes |= COLOR_MODE_CHANGED;
            }

            // This can't change, it's only set at window creation time.
            hideTimeoutMilliseconds = o.hideTimeoutMilliseconds;

            return changes;
        }

```

#### 2번째

frameworks/base/core/java/android/view/WindowManager.java의 3115 줄

[링크](https://cs.android.com/android/platform/superproject/+/master:frameworks/base/core/java/android/view/WindowManager.java;l=78?q=windowmanager&sq=)

```
public String toString(String prefix) {
            StringBuilder sb = new StringBuilder(256);
            sb.append('{');
            dumpDimensions(sb);
            if (horizontalMargin != 0) {
                sb.append(" hm=");
                sb.append(horizontalMargin);
            }
            if (verticalMargin != 0) {
                sb.append(" vm=");
                sb.append(verticalMargin);
            }
            if (gravity != 0) {
                sb.append(" gr=");
                sb.append(Gravity.toString(gravity));
            }
            if (softInputMode != 0) {
                sb.append(" sim={");
                sb.append(softInputModeToString(softInputMode));
                sb.append('}');
            }
            if (layoutInDisplayCutoutMode != 0) {
                sb.append(" layoutInDisplayCutoutMode=");
                sb.append(layoutInDisplayCutoutModeToString(layoutInDisplayCutoutMode));
            }
            sb.append(" ty=");
            sb.append(ViewDebug.intToString(LayoutParams.class, "type", type));
            if (format != PixelFormat.OPAQUE) {
                sb.append(" fmt=");
                sb.append(PixelFormat.formatToString(format));
            }
            if (windowAnimations != 0) {
                sb.append(" wanim=0x");
                sb.append(Integer.toHexString(windowAnimations));
            }
            if (screenOrientation != ActivityInfo.SCREEN_ORIENTATION_UNSPECIFIED) {
                sb.append(" or=");
                sb.append(ActivityInfo.screenOrientationToString(screenOrientation));
            }
            if (alpha != 1.0f) {
                sb.append(" alpha=");
                sb.append(alpha);
            }
            if (screenBrightness != BRIGHTNESS_OVERRIDE_NONE) {
                sb.append(" sbrt=");
                sb.append(screenBrightness);
            }
            if (buttonBrightness != BRIGHTNESS_OVERRIDE_NONE) {
                sb.append(" bbrt=");
                sb.append(buttonBrightness);
            }
            if (rotationAnimation != ROTATION_ANIMATION_ROTATE) {
                sb.append(" rotAnim=");
                sb.append(rotationAnimationToString(rotationAnimation));
            }
            if (preferredRefreshRate != 0) {
                sb.append(" preferredRefreshRate=");
                sb.append(preferredRefreshRate);
            }
            if (preferredDisplayModeId != 0) {
                sb.append(" preferredDisplayMode=");
                sb.append(preferredDisplayModeId);
            }
            if (hasSystemUiListeners) {
                sb.append(" sysuil=");
                sb.append(hasSystemUiListeners);
            }
            if (inputFeatures != 0) {
                sb.append(" if=").append(inputFeatureToString(inputFeatures));
            }
            if (userActivityTimeout >= 0) {
                sb.append(" userActivityTimeout=").append(userActivityTimeout);
            }
            if (surfaceInsets.left != 0 || surfaceInsets.top != 0 || surfaceInsets.right != 0 ||
                    surfaceInsets.bottom != 0 || hasManualSurfaceInsets
                    || !preservePreviousSurfaceInsets) {
                sb.append(" surfaceInsets=").append(surfaceInsets);
                if (hasManualSurfaceInsets) {
                    sb.append(" (manual)");
                }
                if (!preservePreviousSurfaceInsets) {
                    sb.append(" (!preservePreviousSurfaceInsets)");
                }
            }
            if (needsMenuKey == NEEDS_MENU_SET_TRUE) {
                sb.append(" needsMenuKey");
            }
            if (mColorMode != COLOR_MODE_DEFAULT) {
                sb.append(" colorMode=").append(ActivityInfo.colorModeToString(mColorMode));
            }
            sb.append(System.lineSeparator());
            sb.append(prefix).append("  fl=").append(
                    ViewDebug.flagsToString(LayoutParams.class, "flags", flags));
            if (privateFlags != 0) {
                sb.append(System.lineSeparator());
                sb.append(prefix).append("  pfl=").append(ViewDebug.flagsToString(
                        LayoutParams.class, "privateFlags", privateFlags));
            }
            if (systemUiVisibility != 0) {
                sb.append(System.lineSeparator());
                sb.append(prefix).append("  sysui=").append(ViewDebug.flagsToString(
                        View.class, "mSystemUiVisibility", systemUiVisibility));
            }
            if (subtreeSystemUiVisibility != 0) {
                sb.append(System.lineSeparator());
                sb.append(prefix).append("  vsysui=").append(ViewDebug.flagsToString(
                        View.class, "mSystemUiVisibility", subtreeSystemUiVisibility));
            }
            sb.append('}');
            return sb.toString();
        }
```

### 3번째

frameworks/base/core/java/android/view/Window.java의 781줄
[링크](https://cs.android.com/android/platform/superproject/+/master:frameworks/base/core/java/android/view/Window.java)

```
   void adjustLayoutParamsForSubWindow(WindowManager.LayoutParams wp) {
        CharSequence curTitle = wp.getTitle();
        if (wp.type >= WindowManager.LayoutParams.FIRST_SUB_WINDOW &&
                wp.type <= WindowManager.LayoutParams.LAST_SUB_WINDOW) {
            if (wp.token == null) {
                View decor = peekDecorView();
                if (decor != null) {
                    wp.token = decor.getWindowToken();
                }
            }
            if (curTitle == null || curTitle.length() == 0) {
                final StringBuilder title = new StringBuilder(32);
                if (wp.type == WindowManager.LayoutParams.TYPE_APPLICATION_MEDIA) {
                    title.append("Media");
                } else if (wp.type == WindowManager.LayoutParams.TYPE_APPLICATION_MEDIA_OVERLAY) {
                    title.append("MediaOvr");
                } else if (wp.type == WindowManager.LayoutParams.TYPE_APPLICATION_PANEL) {
                    title.append("Panel");
                } else if (wp.type == WindowManager.LayoutParams.TYPE_APPLICATION_SUB_PANEL) {
                    title.append("SubPanel");
                } else if (wp.type == WindowManager.LayoutParams.TYPE_APPLICATION_ABOVE_SUB_PANEL) {
                    title.append("AboveSubPanel");
                } else if (wp.type == WindowManager.LayoutParams.TYPE_APPLICATION_ATTACHED_DIALOG) {
                    title.append("AtchDlg");
                } else {
                    title.append(wp.type);
                }
                if (mAppName != null) {
                    title.append(":").append(mAppName);
                }
                wp.setTitle(title);
            }
        } else if (wp.type >= WindowManager.LayoutParams.FIRST_SYSTEM_WINDOW &&
                wp.type <= WindowManager.LayoutParams.LAST_SYSTEM_WINDOW) {
            // We don't set the app token to this system window because the life cycles should be
            // independent. If an app creates a system window and then the app goes to the stopped
            // state, the system window should not be affected (can still show and receive input
            // events).
            if (curTitle == null || curTitle.length() == 0) {
                final StringBuilder title = new StringBuilder(32);
                title.append("Sys").append(wp.type);
                if (mAppName != null) {
                    title.append(":").append(mAppName);
                }
                wp.setTitle(title);
            }
        } else {
            if (wp.token == null) {
                wp.token = mContainer == null ? mAppToken : mContainer.mAppToken;
            }
            if ((curTitle == null || curTitle.length() == 0)
                    && mAppName != null) {
                wp.setTitle(mAppName);
            }
        }
        if (wp.packageName == null) {
            wp.packageName = mContext.getPackageName();
        }
        if (mHardwareAccelerated ||
                (mWindowAttributes.flags & FLAG_HARDWARE_ACCELERATED) != 0) {
            wp.flags |= FLAG_HARDWARE_ACCELERATED;
        }
    }
```

해시맵과 switchcase 비교.
[view링크](https://cs.android.com/android/platform/superproject/+/master:frameworks/base/core/java/android/view/View.java)
view.java 5391줄 -> 많은 swtich case 구문. 직접 작성해서 비교해보기.
enummap으로도?