<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Mini App</title>
    <style>
      body
      {
        margin: 0;
        padding: 1em;
        background-color:white;
      }
      
      [data-cart-info],
      [data-credit-card] {
        transform: scale(0.78);
    	margin-left: -3.4em;
      }
      
      [data-cc-info] input:focus,
      [data-cc-digits] input:focus {
        outline: none;
      }

      .mdc-card__primary-action,
      .mdc-card__primary-action:hover {
        cursor: auto;
        padding: 20px;
        min-height: inherit;
      }
      
      
      [data-credit-card] [data-card-type] {
        transition: width 1.5s;
        margin-left: calc(100% - 130px);
      }
      
      .is-visa {
        background: linear-gradient(135deg, #622774 0%, #c53364 100%);
      }
      
      .is-mastercard {
        background: linear-gradient(135deg, #65799b 0%, #5e2563 100%);
      }

      .is-visa [data-card-type],
      .is-mastercard [data-card-type] {
        width: auto;
      }

      input.is-invalid,
      .is-invalid input {
        text-decoration: line-through;
      }

      ::placeholder {
        color: #fff;
      }
      [data-cart-info] span{
        display:inline-block;
        vertical-align:middle;
      }
      
      span.material-icons{
        font-size:150px;
      }
      
      [data-credit-card]{
        width:435px;
        min-height:240px;
        border-style:solid;
        border-radius:10px;
        background-color:#5d6874;
      }
      
      [data-card-type]{
        display:block;
        width:120px;
        height:60px;
      }
      
      [data-cc-digits]{
        margin-top:2em;
      }
      
      [data-cc-digits] input{
        color:#FFFFFF;
        font-size:2em;
        line-height:2;
        border:none;
        background:none;
        margin-right:0.5em;
      }
      
      [data-cc-info]{
        margin-top:1em;
      }
      
      [data-cc-info] input{
        color:#FFFFFF;
        font-size:1.2em;
        border:none;
        background:none;
      }
      
      [data-cc-info] input:last-child{
        padding-right:10px;
        float:right;
      }
      
      [data-pay-btn]{
        position:fixed;
        width:90%;
        border-style:solid;
        border-width:1px;
        bottom:20px;
      }
    </style>
  </head>
  <body>
    <div data-cart-info>
      <h1 class="mdc-typography--headline4">
        <span class="material-icons">shopping_cart</span>
        <span data-bill></span>
      </h1>
    </div>
    
    <div data-credit-card class="mdc-card mdc-card--outlined">
      <div class="mdc-card__primary-action">
        <img data-card-type src="http://placehold.it/120x60.png?text=Card" alt="card">
        <div data-cc-digits>
           	<input type="text" size="4" placeholder="----">
          	<input type="text" size="4" placeholder="----">
         	<input type="text" size="4" placeholder="----">
          	<input type="text" size="4" placeholder="----">
        </div>
        
        <div data-cc-info>
           <input type="text" size="20" placeholder="Name Surname">
           <input type="text" size="6" placeholder="MM/YY">
        </div>
      </div>
    </div>
    
    <button type="button" class="mdc-button" data-pay-btn>Pay & Checkout Now</button>
    <script>
      const supportedCards = {
        visa, mastercard
      };
      
      const countries = [
        {
          code: "US",
          currency: "USD",
          country: 'United States'
        },
        {
          code: "NG",
          currency: "NGN",
          country: 'Nigeria'
        },
        {
          code: 'KE',
          currency: 'KES',
          country: 'Kenya'
        },
        {
          code: 'UG',
          currency: 'UGX',
          country: 'Uganda'
        },
        {
          code: 'RW',
          currency: 'RWF',
          country: 'Rwanda'
        },
        {
          code: 'TZ',
          currency: 'TZS',
          country: 'Tanzania'
        },
        {
          code: 'ZA',
          currency: 'ZAR',
          country: 'South Africa'
        },
        {
          code: 'CM',
          currency: 'XAF',
          country: 'Cameroon'
        },
        {
          code: 'GH',
          currency: 'GHS',
          country: 'Ghana'
        }
      ];
      
      const startApp = () => {
        fetchBill()
      };
      /* ----- My JS -----*/
      const appState = {}
      const formatAsMoney = (amount, buyerCountry) => {
        countries.forEach(country => {
          if (country.country === buyerCountry) {
            amount.toLocaleString(country.code, {
              style: 'currency',
              currency: country.currency            
          })
          }
          else {
        amount.toLocaleString('US', {
          style: 'currency',
          currency: country.currency
        })
          }
          
        })
      }
      const flagIfInvalid = (field,isValid) => {
        if (isValid === true) field.classList.remove('is-invalid')
        else field.classList.add('is-invalid')
      }
      
      const expiryDateFormatIsValid = (target) => {
        return target.match(/^\d\d\/\d\d$/)?true:false
      }
      const detectCardType = ( { target } ) => {
        const dcc = document.querySelector('[data-credit-card]')
        const dct = document.querySelector('[data-card-type]')
        
        if (target.value.startsWith('4')) {
          dcc.classList.add('is-visa')
          dct.src = supportedCards.visa
          
          return 'is-visa'
        }
        else dcc.classList.remove('is-visa')
        
        if (target.value.startsWith('5')) {
          dcc.classList.add('is-mastercard')
          dct.src = supportedCards.mastercard
          
          return 'is-mastercard'
        }
        else dcc.classList.remove('is-mastercard')
      }
      const validateCardExpiryDate = ({target}) => {
        const isValid = expiryDateFormatIsValid(target.value)
        const currDate = new Date()
        const splitDate = target.value.split('/')
        const usrInput = new Date(20`${Number(splitDate[1])}`,                                 Number(splitDate[0]))
        const output = isValid && usrInput >= currDate
        if (output) return output && flagIfInvalid(target, output)
        
        return flagIfInvalid(target, output) && output
      }
      const validateCardHolderName = ( { target } ) => {
        const [taget] = target.split(' ')
        
        if (target[0].length < 3 || target[1].length < 3) {
          flagIfInvalid(target, false)
          return false
        }
        else {
          flagIfInvalid(target, true)
          return true
        }
        
      }
      const validateWithLuhn = (digits) => {
        const revDig = digits.reverse().map(x => parseInt(x))
        const getLastValue = revDig.splice(0,1)[0]
        const sum = revDig.reduce((a,b,i) => (i%2!==0?a+b:a+((b*2)%9)||9),0)
        sum += getLastValue
        
        return sum%10===0?true:false
      }
      const validateCardNumber = () => {
        const digitsInputs = document.querySelectorAll('[data-cc-digits] input')
        let digits = ''
        
        Array.prototype.forEach.call(digitsInputs, (item) => {digitsInputs += item.value})
        
        digits = digits.split('')
        const checker = validateWithLuhn(digits)
        const digitCont = document.querySelector('[data-cc-digits]')
        
        if (checker) {
          flagIfInvalid(digitCont, true)
          
          return true
        }
        else {
          flagIfInvalid(digitCont, false)
          
          return false
        }
        
      }
      	 function valid_credit_card(value) {
  		if (/[^0-9-\s]+/.test(value)) return false;
		var nCheck = 0, nDigit = 0, bEven = false;
		value = value.replace(/\D/g, "");

		for (var n = value.length - 1; n >= 0; n--) {
		var cDigit = value.charAt(n),
			  nDigit = parseInt(cDigit, 10);

		if (bEven) {
			if ((nDigit *= 2) > 9) nDigit -= 9;
		}

		nCheck += nDigit;
		bEven = !bEven;
	}

		return (nCheck % 10) == 0;
	}
      const uiCanInteract = () => {
        let cardOne = document.querySelector('[data-cc-digits] input:nth-child(1)')
        let cardHolder = document.querySelector('[data-cc-info] input:nth-child(1)')
        let cardXpr = document.querySelector('[data-cc-info] input:nth-child(2)')
        let btn = document.querySelector('button')
        
        cardOne.addEventListener('blur', detectCardType)
        cardHolder.addEventListener('blur', validateCardHolderName)
        cardXpr.addEventListener('blur', validateCardExpiryDate)
        btn.addEventListener('click', validateCardNumber)
        cardOne.focus()
        
      }
      const displayCartTotal = ( { results } ) => {
        const [data] = results        
        const { itemsInCart, buyerCountry } = data
        
        appState.items = itemsInCart
        appState.country = buyerCountry
        
        appState.bill = itemsInCart.reduce((total,item) => { return total + (item.price * item.qty)}, 0)
        
        appState.billFormatted = formatAsMoney(appState.bill,appState.country)
        console.log('bill formated' ,appSate.billFormatted)
        
        document.querySelector('[data-bill]').textContent = appState.billFormatted
        
        uiCanInteract()
        
      }
      const fetchBill = () => {
        const api = 'https://randomapi.com/api/006b08a801d82d0c9824dcfdfdfa3b3c'
        fetch(api)
        .then(response => response.json())
        .then(data => displayCartTotal(data))
        .catch(err => console.log('Fetch Error : -S', err))
        
        
        console.log(displayCartTotal('our db' ,data))
      }
     
      startApp();
    </script>
  </body>
</html>

