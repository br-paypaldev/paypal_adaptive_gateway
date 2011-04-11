Paypal Adaptive Payments Library for ActiveMerchant
====================================================

Suporta pagamentos, pre-aprovação, reembolso e conversão de moedas.

Requisitos
--------------
  .Rails 3.0
  .Json
  .Builder
  .ActiveMerchant 1.9.x

Instalando
--------------
    git clone https://github.com/varsh/paypal_adaptive_gateway.git
    cd paypal_adaptive_gateway
    rails <Path to active merchant library> ex: "C:\Ruby187\lib\ruby\gems\1.8\gems\activemerchant-1.9.1"
  
Usando
------------------
  no application_controller.rb
  
    def gateway
      @gateway ||= PaypalAdaptivePaymentGateway.new(
       :login => 'seu_email',
       :password => 'sua_senha',
       :signature => ' sua_signature',
       :appid => 'seu_appID'
      )
    end
  
  No processo de pagamento:
  
    #for chained payments
    def checkout
      recipients = [{:email => 'email_recebedor',
                     :amount => some_amount,
                     :primary => true},
                    {:email => 'email_recebedor',
                     :amount => recipient_amount,
                     :primary => false}
                     ]
      response = gateway.pay(
        :return_url => url_for(:action => 'action', :only_path => false),
        :cancel_url => url_for(:action => 'action', :only_path => false),
        :notify_url => url_for(:action => 'notify_action', :only_path => false),
        :receiver_list => recipients
      )
      redirect_to response.redirect_url_for
    end
    
    set the :primary flag to false for each recipient for a split payment

  No processo de reembolso:
    recipients = [{:email => params[:receiveremail_0],
                     :amount => params[:amount_0]}
                     ]

      response = gateway.refund(
        :return_url => "returnURL",
        :cancel_url => "cancelURL",
	  :pay_key=>params[:paykey],
        :receiver_list => recipients
      )
    
  No processo de aprovação prévia:
    response = gateway.preapprove_payment(
        :return_url => "returnURL",
        :cancel_url => "cancelURL",
        :senderEmail =>"email address of sender",
        :start_date =>"starting date",
        :end_date =>"ending date",
        :currency_code =>"currency code",
        :max_amount =>"maxTotalAmountOfAllPayments",
        :maxNumberOfPayments =>"maxNumberOfPayments"
    )

  No processo de cancelamento de aprovação prévia:
	response = gateway.cancel_preapproval(
	:preapproval_key =>"preapprovalkey"
      )

  para debugar
  
    utilize tanto o gateway.debug quanto o response.debug, isso lhe dará a resposta JSON, o XML enviado e a URL que foi postado.
