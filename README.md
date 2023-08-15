
# Bluecoat
# https://sitereview.bluecoat.com/#/lookup-result/facebook.com
# Palo
# https://urlfiltering.paloaltonetworks.com/single_cr/?url=facebook.com
# Google Safe Browsing site status
# https://transparencyreport.google.com/safe-browsing/search?url=youtube-cookies.com&hl=en
# VirusTotal
# https://www.virustotal.com/gui/domain/facebook.com
# Norton
# https://safeweb.norton.com/report/show?url=facebook.com
# Who.is
# https://who.is/whois/chat.openai.com


class UrlReview:
    
    def __init__(self):
        # Define a list with your domain strings
        self.domains = ["google.com", "facebook.com"]

        # Base URLs
        self.webpulse_base_url = "https://sitereview.bluecoat.com/#/lookup-result/"
        self.paloalto_base_url = "https://urlfiltering.paloaltonetworks.com/single_cr/?url="
        self.norton_base_url = "https://safeweb.norton.com/report/show?url="

    def webpulse(self):
        # Iterate over each domain and append it to the base URL
        for domain in self.domains:
            complete_url = self.webpulse_base_url + domain
            print(complete_url)

    def paloalto(self):
        # Iterate over each domain and append it to the base URL
        for domain in self.domains:
            complete_url = self.paloalto_base_url + domain
            print(complete_url)
            
    def norton(self):
        # Iterate over each domain and append it to the base URL
        for domain in self.domains:
            complete_url = self.norton_base_url + domain
            print(complete_url)

    def run(self):
        # Order of above functions being called
        self.webpulse()
        self.paloalto()
        self.norton()

if __name__ == '__main__':
    review = UrlReview()
    review.run()
