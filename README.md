# scan-sca-action
Scan Software Composition Analysis for vulnerabilities action for github/gitea workflows

## Usage

### Scan a docker image from docker hub
```
jobs:
  scan:    
    - name: Run the FireClover SCA scan action
      uses: fc-actions/scan-sca-action@v0.1.5
      with:
        source: node:18
```

### Simple SCA scan of source folder
```
jobs:
  build:    
    - name: 'Install, build and test NodeJS application'
      run: npm ci && npm run build && npm run test

    - name: Run the FireClover SCA scan action
      uses: fc-actions/scan-sca-action@v0.1.5
      id: sca
      continue-on-error: true
      with:
        source: .
```

### Simple SCA scan of locally built docker image with upload results using FireClover report action
```
jobs:
  build:    
    - name: 'Install, build and test application with Docker image'
      run: echo 'See FireClover Docker build action for help'

    - name: Run the FireClover SCA scan action
      uses: fc-actions/scan-sca-action@v0.1.5
      id: sca
      continue-on-error: true
      with:
        source: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.ref_name }}

    - name: Upload vulnerability report
      needs: [steps.sca]
      uses: fc-actions/report-action@v0.1.11
      continue-on-error: true
      with:
        create-repo-issues: 'Critical High'
        instance-url: ${{ vars.FARADAY_URL }}
        password: ${{ secrets.FARADAY_PASSWD }}
        workspace: ${{ env.CUSTOMER_ID || env.STAR_DEPLOYMENT_VANITY_SUBDOMAIN }}
        result-file: ${{ steps.sca.outputs.vulns }}
```
