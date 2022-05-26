---
title: Immediately-Invoked Function Expression
author: ianpowell
date: 2016-07-23 18:54:00 +0100
categories: [.net]
tags: [aspnet]
---

``` javascript
(function ($) {
        var model = {
            init: function () {
            }
        },
            view = {
                init: function () {
                    model.init();
                    this.$btnSendCode = $('#btnSendCode').click(this.btnSendCodeClick);
                    this.$txtUsername = $('#Username');
                    this.$modal = $('#modal').on('shown.bs.modal', this.reAlignModal);
                    this.$modalBodyText = this.$modal.find('.modal-body');
                    $(window).on("resize", function () {
                        $(".modal:visible").each(view.reAlignModal);
                    });
                    $("#modalTemplate").template("modalTemplate");
                },
                $txtUsername: null,
                $btnSendCode: null,
                btnSendCodeClick: function () {
                    var mn = view.$txtUsername.val();
                    controller.sendCodeRequest(mn);
                },
                $modal: null,
                $modalHeader: null,
                $modalBodyText: null,
                modalShow: function (dataObject) {
                    $.tmpl("modalTemplate", dataObject).appendTo(this.$modalBodyText.empty());
                    this.$modal.modal('show');
                },
                reAlignModal: function () {
                    var $modalDialog = $(this).find(".modal-dialog");
                    $modalDialog.css("margin-top", Math.max(0, ($(window).height() - $modalDialog.height()) / 3));
                }
            },
            controller = {
                init: function () {
                    view.init();
                },
                sendCodeRequest: function (mobileNumber) {
                    var postData = {
                        mobileNumber: mobileNumber
                    };
                    addAntiForgeryToken(postData);
                    $.ajax({
                        cache: false,
                        type: "POST",
                        url: "@Url.Action("SendReminder", "CustomCustomer")",
                        data: postData,
                        dataType: "json",
                        success: controller.sendCodeResponseSuccess,
                        error: controller.sendCodeResponseError
                    });
                },
                sendCodeResponseSuccess: function (data, textStatus, jqXHR) {
                    if (data.isDistributorCustomer) {
                        view.modalShow({
                            distributor: {
                                name: data.distributorCompanyName,
                                email: data.distributorEmailAddress,
                                phone: data.distributorPhoneNumber,
                                hasName: data.distributorCompanyName !== null
                            }
                        });
                    } else {
                        view.modalShow({
                            isSent: true
                        });
                    }

                },
                sendCodeResponseError: function (jqXHR, textStatus, errorThrown) {
                    if (jqXHR.responseJSON == null || jqXHR.responseJSON.Error == null)
                        return;

                    view.modalShow({
                        error: jqXHR.responseJSON.Error
                    });
                }
            };
        $(document).ready(controller.init);
    }(window.jQuery));
```

``` javascript
// CSRF (XSRF) security
function addAntiForgeryToken(data) {
    //if the object is undefined, create a new one.
    if (!data) {
        data = {};
    }
    //add token
    var tokenInput = $('input[name=__RequestVerificationToken]');
    if (tokenInput.length) {
        data.__RequestVerificationToken = tokenInput.val();
    }
    return data;
};
```
