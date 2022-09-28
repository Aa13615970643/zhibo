<template>
    <view :class="extClass">
        <slot></slot>
    </view>
</template>

<script>
Object.defineProperty(exports, '__esModule', {
    value: true
});

var form_validator_1 = __webpack_require__(5);

var object_1 = __webpack_require__(0);

function linked(target) {
    if (target.data.prop) {
        this.formItems[target.data.prop] = target;
    }

    if (target.setInForm) {
        target.setInForm();
    }

    if (!this.firstItem) {
        this.firstItem = target;
    }

    if (target !== this.firstItem) {
    }
}

function unlinked(target) {
    if (target.data.prop) {
        delete this.formItems[target.data.prop];
    }
}

export default {
    data() {
        return {
            errors: {},
            formItems: {},
            firstItem: null,
            newRules: ''
        };
    },
    props: {
        models: {
            type: Object,
            default: () => ({})
        },
        rules: {
            type: Array,
            default: () => []
        },
        extClass: {
            type: String,
            default: ''
        }
    },
    relations: {
        '../cell/cell': {
            type: 'descendant',
            linked: linked,
            unlinked: unlinked
        },
        '../checkbox-group/checkbox-group': {
            type: 'descendant',
            linked: linked,
            unlinked: unlinked
        }
    },
    beforeMount: function attached() {
        this.initRules();
        this.formValidator = new form_validator_1.default(this.models, this.newRules);
    },
    methods: {
        initRules: function initRules(rules) {
            var newRules = {};
            (rules || this.rules).forEach(function (rule) {
                if (rule.name && rule.rules) {
                    newRules[rule.name] = rule.rules || [];
                }
            });
            this.setData({
                newRules: newRules
            });
            return newRules;
        },
        _modelChange: function _modelChange(newVal, oldVal, path) {
            var that = this;

            if (!this.isInit) {
                this.isInit = true;
                return newVal;
            }

            this.formValidator.setModel(newVal);
            this.isInit = true;
            var diffObj = object_1.diffObject(oldVal, newVal);

            if (diffObj) {
                (function () {
                    var isValid = true;
                    var errors = [];
                    var errorMap = {};

                    var _loop = function _loop(k) {
                        that.formValidator.validateField(k, diffObj[k], function (isValided, error) {
                            if (error && error[k]) {
                                errors.push(error[k]);
                                errorMap[k] = error[k];
                            }

                            isValid = isValided;
                        });
                    };

                    for (var k in diffObj) {
                        _loop(k);
                    }

                    that._showErrors(diffObj, errorMap);

                    that.$emit(isValid ? 'success' : 'fail', {
                        detail: isValid
                            ? {
                                  trigger: 'change'
                              }
                            : {
                                  errors: errors,
                                  trigger: 'change'
                              }
                    });
                })();
            }

            return newVal;
        },
        _rulesChange: function _rulesChange(newVal) {
            var newRules = this.initRules(newVal);

            if (this.formValidator) {
                this.formValidator.setRules(newRules);
            }

            return newVal;
        },
        _showAllErrors: function _showAllErrors(errors) {
            for (var k in this.newRules) {
                this._showError(k, errors && errors[k]);
            }
        },
        _showErrors: function _showErrors(objs, errors) {
            for (var k in objs) {
                this._showError(k, errors && errors[k]);
            }
        },
        _showError: function _showError(prop, error) {
            var formItem = this.formItems[prop];

            if (formItem && formItem.data.showError) {
                formItem.setError(error);
            }
        },
        validate: function validate(cb) {
            var that = this;
            return this.formValidator.validate(function (isValid, errors) {
                that._showAllErrors(errors);

                var handleError = that.handleErrors(errors);
                that.$emit(isValid ? 'success' : 'fail', {
                    detail: isValid
                        ? {
                              trigger: 'validate'
                          }
                        : {
                              errors: handleError,
                              trigger: 'validate'
                          }
                });

                if (cb) {
                    cb(isValid, handleError);
                }
            });
        },
        validateField: function validateField(name, value) {
            var that = this;
            var cb =
                arguments.length > 2 && arguments[2] !== undefined
                    ? arguments[2]
                    : function (v) {
                          var error = arguments.length > 1 && arguments[1] !== undefined ? arguments[1] : null;
                      };
            return this.formValidator.validateField(name, value, function (isValid, errors) {
                that._showError(name, errors);

                var handleError = that.handleErrors(errors);
                that.$emit(isValid ? 'success' : 'fail', {
                    detail: isValid
                        ? {
                              trigger: 'validate'
                          }
                        : {
                              errors: handleError,
                              trigger: 'validate'
                          }
                });

                if (cb) {
                    cb(isValid, handleError);
                }
            });
        },
        handleErrors: function handleErrors(errors) {
            if (errors) {
                var newErrors = [];
                this.rules.forEach(function (rule) {
                    if (errors[rule.name]) {
                        errors[rule.name].name = rule.name;
                        newErrors.push(errors[rule.name]);
                    }
                });
                return newErrors;
            }

            return errors;
        },
        addMethod: function addMethod(ruleName, method) {
            return this.formValidator.addMethod(ruleName, method);
        }
    },
    watch: {
        models: {
            handler: function _modelChange(newVal, oldVal, path) {
                var that = this;

                if (!this.isInit) {
                    this.isInit = true;
                    return newVal;
                }

                this.formValidator.setModel(newVal);
                this.isInit = true;
                var diffObj = object_1.diffObject(oldVal, newVal);

                if (diffObj) {
                    (function () {
                        var isValid = true;
                        var errors = [];
                        var errorMap = {};

                        var _loop = function _loop(k) {
                            that.formValidator.validateField(k, diffObj[k], function (isValided, error) {
                                if (error && error[k]) {
                                    errors.push(error[k]);
                                    errorMap[k] = error[k];
                                }

                                isValid = isValided;
                            });
                        };

                        for (var k in diffObj) {
                            _loop(k);
                        }

                        that._showErrors(diffObj, errorMap);

                        that.$emit(isValid ? 'success' : 'fail', {
                            detail: isValid
                                ? {
                                      trigger: 'change'
                                  }
                                : {
                                      errors: errors,
                                      trigger: 'change'
                                  }
                        });
                    })();
                }

                return newVal;
            },

            immediate: true,
            deep: true
        },

        rules: {
            handler: function _rulesChange(newVal) {
                var newRules = this.initRules(newVal);

                if (this.formValidator) {
                    this.formValidator.setRules(newRules);
                }

                return newVal;
            },

            immediate: true,
            deep: true
        }
    }
};
exports.default = form_validator_1.default;
/***/
</script>
<style></style>
