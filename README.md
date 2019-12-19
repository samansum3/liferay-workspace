# liferay-workspace
<%@ include file="/html/init.jsp" %>

<portlet:resourceURL id="/post-activity" var="portletResourceUrl" />
<portlet:resourceURL id="/post-activity/fetch_posts" var="fetchPostsUrl" />
<liferay-util:html-top>
    <style>
      @media only screen and (max-width: 768px) {
        section[id^="portlet_com_liferay_content_targeting_display_web_portlet_UserSegmentContentListPortlet"] {
          display: none;
        }
      }
    </style>
</liferay-util:html-top>
<%if(postActivityDisplayContext.hasViewPermission()){%>
    <div class="post-activity-feed-wrapper">
        <%if (postActivityDisplayContext.hasUpdatePermission() && !isUserHomePage) {%>
            <liferay-portlet:runtime portletName="<%= PostActivityWebPortletKeys.PostActivityMini %>" />
        <%}%>
        <div id="<portlet:namespace />activityFeed" class="cec-activity-feed">
            <div :class="{'col-md-6': showUserHomePage}" v-if="!showUserHomePage || !isMobile">
                <div v-for="(postActivityInfo, index) in postActivityInfoList">
                    <div v-if="isMobile && index >= 3 && index % 3 == 0">
                        <div v-if="index == 3">
                            <div class="partner-block" v-if="partner>
                                <div class="partner-block__body aspect-ratio-bg-cover" :style="{ backgroundImage: 'url(' + partner.image + ')'}">
                                    <p class="lead-text">{{ partner.message }}</p>
                                    <a :href="partner.link" class="btn btn-light" target="_blank">
                                        {{ partner.label }}
                                    </a>
                                </div>
                            </div>
                        </div>
                        <promotion
                            v-else
                            :promotions="promotions.list"
                            :next-index="(index - 3) / 3 * 2"
                        ></promotion>
                    </div>
                    <post-activity
                        portlet-namespace="<portlet:namespace />"
                        :post-activity-info="postActivityInfo"
                        portlet-resource-url="<%= portletResourceUrl %>"
                        :path-theme-images="pathThemeImages"
                        v-on:share-post-popup="openSharePostPopup"
                    >
                    </post-activity>
                </div>
                <div v-if="fetchingPosts" class="text-center mb-3">
                    <svg class="lexicon-icon icon-loading-lg">
                        <use :xlink:href="pathThemeImages + '/icons/icons.svg#loading'" />
                    </svg>
                </div>
            </div>
            <div class="col-md-6" v-if="showUserHomePage">
                <profile-summary
                    :path-theme-images="pathThemeImages"
                    user-name="Jon Watts"
                    description="I enjoy enabling organisations with great technology platforms and have 20 years experience in technology..."
                    number-of-connection="12"
                    number-of-community="12"
                    number-of-request="3"
                    number-of-post="3"
                    connection-url=""
                    my-community-url=""
                    connection-request-url=""
                ></<profile-summary>
                <flow-of-training :flow-of-training="[]"></flow-of-training>
                <what-is-new class="hidden-xs" :what-is-new="{}"></what-is-new>
                <your-learning class="hidden-xs" :your-learning="{}"></your-learning>
            </div>

            <div v-if="showUserHomePage && isMobile">
                <div v-for="(postActivityInfo, index) in postActivityInfoList">
                    <div v-if="index >= 3 && index % 3 == 0">
                        <what-is-new
                            v-if="index == 6"
                            :what-is-new="{}"
                        ></what-is-new>
                        <promotion
                            v-else
                            :promotions="promotions.list"
                            :next-index="index > 6 ? (index - 3) : index / 3 * 2"
                        ></promotion>
                        <your-learning
                            v-if="index == 9"
                            :your-learning="{}"
                        ></your-learning>
                    </div>
                    <post-activity
                        portlet-namespace="<portlet:namespace />"
                        :post-activity-info="postActivityInfo"
                        portlet-resource-url="<%= portletResourceUrl %>"
                        :path-theme-images="pathThemeImages"
                        v-on:share-post-popup="openSharePostPopup"
                    ></post-activity>
                </div>
                <div v-if="fetchingPosts" class="text-center mb-3">
                    <svg class="lexicon-icon icon-loading-lg">
                        <use :xlink:href="pathThemeImages + '/icons/icons.svg#loading'" />
                    </svg>
                </div>
            </div>
        </div>
        <aui:script require="<%= mainRequire %>">
            main.default(
                '<portlet:namespace />',
                <%= postActivityDisplayContext.getActOnGivingAsJson() %>,
                ${promotions},
                <%= postActivityDisplayContext.getTotalPosts() %>,
                '<%= fetchPostsUrl %>',
                <%= isUserHomePage %>
            );
        </aui:script>
    </div>
<%}%>
